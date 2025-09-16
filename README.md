<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Duolingo de História</title>
  <style>
    body {
      font-family: 'Comic Sans MS', cursive, sans-serif;
      background: pink;
      text-align: center;
      margin: 0;
      padding: 0;
    }
    h1 {
      color: #ff1493;
      margin: 20px;
      font-size: 32px;
    }
    .container {
      max-width: 600px;
      margin: auto;
      padding: 20px;
      border: 5px solid yellow;
      border-radius: 20px;
      background: white;
    }
    input {
      width: 90%;
      padding: 10px;
      margin: 8px 0;
      border: 2px solid #ffb6c1;
      border-radius: 12px;
      font-size: 16px;
    }
    button {
      background: #ff69b4;
      border: none;
      border-radius: 12px;
      padding: 12px;
      margin: 10px 0;
      font-size: 16px;
      cursor: pointer;
      color: white;
      width: 200px;
      transition: background 0.2s;
    }
    button:hover {
      background: #ff1493;
    }
    .hidden {
      display: none;
    }
    .fase-mapa {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 20px;
      margin-top: 30px;
    }
    .fase {
      background: #fff5ba;
      border: 3px solid #ffb6c1;
      border-radius: 12px;
      width: 120px;
      height: 120px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-weight: bold;
      color: #ff80aa;
      cursor: pointer;
      transition: transform 0.2s;
    }
    .fase:hover {
      transform: scale(1.1);
    }
    .quiz-container {
      background: white;
      border-radius: 20px;
      padding: 20px;
      box-shadow: 0 0 10px rgba(0,0,0,0.2);
    }
    .quiz-container button {
      width: 100%;
      margin: 5px 0;
    }
    .feedback {
      margin-top: 15px;
      font-weight: bold;
    }
    .pontos {
      font-size: 18px;
      color: #ff1493;
      margin-top: 10px;
    }
    .toggle-senha {
      cursor: pointer;
      font-size: 12px;
      color: #888;
      display: block;
      margin-top: -5px;
    }
    .salvar-conta {
      background: #ffffcc;
      padding: 10px;
      border-radius: 8px;
      margin-bottom: 10px;
      display: none;
    }
  </style>
</head>
<body>
  <h1>🌸 Duolingo de História 🌸</h1>

  <!-- Cadastro/Login -->
  <div id="login" class="container">
    <div id="salvarConta" class="salvar-conta">
      <p>Salvar conta?</p>
      <button onclick="decidirSalvar(true)">Sim</button>
      <button onclick="decidirSalvar(false)">Não</button>
    </div>
    <h2>Entrar ou Cadastrar</h2>
    <input type="email" id="email" placeholder="Digite seu e-mail"><br>
    <input type="password" id="senha" placeholder="Digite sua senha"><br>
    <span class="toggle-senha" onclick="toggleSenha()">👁 Ver senha</span><br>
    <button onclick="cadastrar()">Cadastrar</button>
    <button onclick="entrar()">Entrar</button>
    <p id="loginMsg"></p>
  </div>

  <!-- Mapa de Fases -->
  <div id="mapa" class="hidden container">
    <h2>Escolha uma fase</h2>
    <p class="pontos">⭐ Pontos: <span id="pontos">0</span></p>
    <div class="fase-mapa">
      <div class="fase" onclick="iniciarFase('Dom Pedro I')">Dom Pedro I</div>
      <div class="fase" onclick="iniciarFase('Dom Pedro II')">Dom Pedro II</div>
      <div class="fase" onclick="iniciarFase('Princesa Isabel')">Princesa Isabel</div>
      <div class="fase" onclick="iniciarFase('Canudos')">Canudos</div>
      <div class="fase" onclick="iniciarFase('Vietnã')">Vietnã</div>
      <div class="fase" onclick="iniciarFase('Segunda Guerra')">2ª Guerra</div>
      <div class="fase" onclick="iniciarFase('Revolução Francesa')">Rev. Francesa</div>
    </div>
  </div>

  <!-- Quiz -->
  <div id="quiz" class="container hidden">
    <div class="quiz-container">
      <h2 id="fase-titulo"></h2>
      <p id="pergunta"></p>
      <div id="opcoes"></div>
      <p class="feedback" id="feedback"></p>
      <button id="passar" class="hidden" onclick="proximaPergunta()">➡️ Passar de fase</button>
      <button id="explicacaoBtn" class="hidden" onclick="mostrarExplicacao()">📖 Explicação</button>
      <p id="textoExplicacao" class="hidden"></p>
    </div>
  </div>

  <script>
    let usuarios = JSON.parse(localStorage.getItem("usuarios")) || {};
    let usuarioLogado = null;
    let pontos = 0;

    let salvo = localStorage.getItem("loginSalvo");
    if (salvo && usuarios[salvo]) {
      usuarioLogado = salvo;
      document.addEventListener("DOMContentLoaded", () => {
        document.getElementById("login").classList.add("hidden");
        document.getElementById("mapa").classList.remove("hidden");
      });
    }

    function toggleSenha() {
      let senha = document.getElementById("senha");
      senha.type = senha.type === "password" ? "text" : "password";
    }

    function cadastrar() {
      let email = document.getElementById("email").value;
      let senha = document.getElementById("senha").value;
      let msg = document.getElementById("loginMsg");
      if (!email || !senha) {
        msg.innerText = "❌ Preencha todos os campos!";
        msg.style.color = "red";
        return;
      }
      if (!email.includes("@")) {
        msg.innerText = "❌ E-mail inválido!";
        msg.style.color = "red";
        return;
      }
      if (usuarios[email]) {
        msg.innerText = "❌ Já existe uma conta com este e-mail.";
        msg.style.color = "red";
      } else {
        usuarios[email] = senha;
        localStorage.setItem("usuarios", JSON.stringify(usuarios));
        msg.innerText = "✅ Cadastro feito com sucesso!";
        msg.style.color = "green";
        document.getElementById("salvarConta").style.display = "block";
        usuarioLogado = email;
      }
    }

    function decidirSalvar(sim) {
      document.getElementById("salvarConta").style.display = "none";
      if (sim) {
        localStorage.setItem("loginSalvo", usuarioLogado);
      } else {
        localStorage.removeItem("loginSalvo");
      }
      document.getElementById("login").classList.add("hidden");
      document.getElementById("mapa").classList.remove("hidden");
    }

    function entrar() {
      let email = document.getElementById("email").value;
      let senha = document.getElementById("senha").value;
      let msg = document.getElementById("loginMsg");
      if (usuarios[email] && usuarios[email] === senha) {
        usuarioLogado = email;
        document.getElementById("login").classList.add("hidden");
        document.getElementById("mapa").classList.remove("hidden");
      } else {
        msg.innerText = "❌ E-mail ou senha incorretos.";
        msg.style.color = "red";
      }
    }

    // 🔥 Todas as fases com 10 perguntas cada
    let fases = {
      "Dom Pedro I": [
        { pergunta: "Em que ano Dom Pedro I proclamou a Independência do Brasil?", opcoes: ["1822", "1808", "1889", "1763"], correta: 0, explicacao: "A Independência do Brasil foi proclamada em 7 de setembro de 1822." },
        { pergunta: "Qual era o lema famoso no Grito do Ipiranga?", opcoes: ["Ordem e Progresso", "Independência ou Morte!", "Brasil acima de tudo", "Tudo pela Nação"], correta: 1, explicacao: "Dom Pedro I disse 'Independência ou Morte!' no Grito do Ipiranga." },
        { pergunta: "Qual era o título oficial de Dom Pedro I?", opcoes: ["Imperador do Brasil", "Rei de Portugal", "Presidente do Brasil", "Duque de Bragança"], correta: 0, explicacao: "Dom Pedro I foi o primeiro Imperador do Brasil." },
        { pergunta: "Quem era a esposa de Dom Pedro I?", opcoes: ["Maria Leopoldina", "Isabel", "Maria II", "Carlota Joaquina"], correta: 0, explicacao: "Maria Leopoldina foi esposa de Dom Pedro I." },
        { pergunta: "Dom Pedro I também foi rei de qual país europeu?", opcoes: ["Portugal", "Espanha", "França", "Inglaterra"], correta: 0, explicacao: "Dom Pedro I foi também Dom Pedro IV, rei de Portugal." },
        { pergunta: "Qual foi a primeira capital do Brasil independente?", opcoes: ["Rio de Janeiro", "Brasília", "Salvador", "São Paulo"], correta: 2, explicacao: "A primeira capital do Brasil independente foi Salvador." },
        { pergunta: "Em que ano Dom Pedro I abdicou do trono brasileiro?", opcoes: ["1831", "1822", "1840", "1889"], correta: 0, explicacao: "Dom Pedro I abdicou em favor de seu filho Dom Pedro II, em 1831." },
        { pergunta: "Quem assumiu como imperador após a abdicação de Dom Pedro I?", opcoes: ["Dom Pedro II", "Isabel", "José Bonifácio", "Dom João VI"], correta: 0, explicacao: "Dom Pedro II assumiu o trono após a abdicação de seu pai." },
        { pergunta: "Dom Pedro I enfrentou revoltas durante seu governo?", opcoes: ["Sim", "Não", "Apenas uma", "Não se sabe"], correta: 0, explicacao: "Dom Pedro I enfrentou várias revoltas, como a Confederação do Equador." },
        { pergunta: "Qual foi a relação de Dom Pedro I com a Constituição de 1824?", opcoes: ["Ele outorgou", "Ele rejeitou", "Ele ignorou", "Não existiu"], correta: 0, explicacao: "Dom Pedro I outorgou a primeira Constituição do Brasil em 1824." }
      ],
      "Dom Pedro II": [
        { pergunta: "Por quantos anos Dom Pedro II governou o Brasil?", opcoes: ["10 anos", "49 anos", "22 anos", "5 anos"], correta: 1, explicacao: "Dom Pedro II governou por 49 anos, de 1840 a 1889." },
        { pergunta: "Qual evento marcou o fim de seu reinado?", opcoes: ["Proclamação da República", "Abolição da escravidão", "Guerra do Paraguai", "Grito do Ipiranga"], correta: 0, explicacao: "O reinado de Dom Pedro II terminou com a Proclamação da República, em 1889." },
        { pergunta: "Dom Pedro II era conhecido por ser:", opcoes: ["Curioso e estudioso", "Guerreiro", "Recluso", "Comerciante"], correta: 0, explicacao: "Dom Pedro II era muito estudioso e amante das ciências." },
        { pergunta: "Qual guerra importante ocorreu durante seu governo?", opcoes: ["Guerra do Paraguai", "Guerra do Vietnã", "Revolução Francesa", "Segunda Guerra"], correta: 0, explicacao: "A Guerra do Paraguai ocorreu entre 1864 e 1870 durante seu governo." },
        { pergunta: "Como era chamado o período do reinado de Dom Pedro II?", opcoes: ["Segundo Reinado", "Primeiro Reinado", "Era Vargas", "Império Novo"], correta: 0, explicacao: "O período é chamado de Segundo Reinado." },
        { pergunta: "Dom Pedro II apoiava a abolição da escravidão?", opcoes: ["Sim", "Não", "Não se sabe", "Apenas parcialmente"], correta: 0, explicacao: "Ele apoiava a abolição, mas respeitava o processo legal e político." },
        { pergunta: "Quem foi a filha de Dom Pedro II que assinou a Lei Áurea?", opcoes: ["Princesa Isabel", "Maria Leopoldina", "Carlota Joaquina", "Ana de Orleans"], correta: 0, explicacao: "Princesa Isabel assinou a Lei Áurea." },
        { pergunta: "Em que ano Dom Pedro II assumiu como imperador?", opcoes: ["1840", "1822", "1831", "1889"], correta: 0, explicacao: "Ele assumiu em 1840, após uma regência." },
        { pergunta: "Dom Pedro II viajou para conhecer outros países?", opcoes: ["Sim", "Não", "Só Portugal", "Apenas França"], correta: 0, explicacao: "Ele viajou pela Europa para estudar e conhecer outros países." },
        { pergunta: "Como terminou o reinado de Dom Pedro II?", opcoes: ["Exílio", "Foi assassinado", "Morreu no trono", "Renunciou"], correta: 0, explicacao: "Ele foi deposto e exilado após a Proclamação da República." }
      ],
      "Princesa Isabel": [
        { pergunta: "Quem assinou a Lei Áurea?", opcoes: ["Princesa Isabel", "Dom Pedro II", "José Bonifácio", "Dom Pedro I"], correta: 0, explicacao: "Princesa Isabel assinou a Lei Áurea em 1888." },
        { pergunta: "Em que ano foi assinada a Lei Áurea?", opcoes: ["1888", "1822", "1889", "1871"], correta: 0, explicacao: "A Lei Áurea foi assinada em 13 de maio de 1888." },
        { pergunta: "A Lei Áurea aboliu o quê?", opcoes: ["Escravidão", "Impostos", "Monarquia", "Corveia"], correta: 0, explicacao: "Ela aboliu a escravidão no Brasil." },
        { pergunta: "Qual era o título de Isabel?", opcoes: ["Princesa Imperial", "Rainha", "Duquesa", "Imperatriz"], correta: 0, explicacao: "Isabel era Princesa Imperial do Brasil." },
        { pergunta: "Quem era o pai da Princesa Isabel?", opcoes: ["Dom Pedro II", "Dom Pedro I", "José Bonifácio", "Napoleão"], correta: 0, explicacao: "O pai dela era Dom Pedro II." },
        { pergunta: "Quantos filhos Isabel teve?", opcoes: ["3", "0", "1", "2"], correta: 1, explicacao: "Princesa Isabel não teve filhos." },
        { pergunta: "A assinatura da Lei Áurea gerou?", opcoes: ["Fim da escravidão", "Fim da monarquia", "Guerra civil", "Nada"], correta: 0, explicacao: "Foi o fim da escravidão no Brasil." },
        { pergunta: "Isabel recebeu algum apelido popular?", opcoes: ["Redentora", "Libertadora", "Imperatriz", "Rainha do Brasil"], correta: 0, explicacao: "Ela era chamada de 'A Redentora'." },
        { pergunta: "A Lei Áurea foi sancionada pela:", opcoes: ["Princesa Isabel", "Assembleia", "Dom Pedro II", "Câmara Municipal"], correta: 0, explicacao: "A princesa sancionou pessoalmente a lei." },
        { pergunta: "A assinatura da Lei Áurea marcou qual período?", opcoes: ["Final do Império", "Início da República", "Descobrimento", "Independência"], correta: 0, explicacao: "Foi um marco do final do período imperial brasileiro." }
      ]
    };

    let faseAtual = null;
    let perguntaAtual = 0;

    function iniciarFase(nome) {
      faseAtual = nome;
      perguntaAtual = 0;
      document.getElementById("mapa").classList.add("hidden");
      document.getElementById("quiz").classList.remove("hidden");
      document.getElementById("fase-titulo").innerText = "Fase: " + nome;
      carregarPergunta();
    }

    function carregarPergunta() {
      let q = fases[faseAtual][perguntaAtual];
      document.getElementById("pergunta").innerText = q.pergunta;
      let opcoesDiv = document.getElementById("opcoes");
      opcoesDiv.innerHTML = "";
      q.opcoes.forEach((op, i) => {
        let btn = document.createElement("button");
        btn.innerText = op;
        btn.onclick = () => verificarResposta(i);
        opcoesDiv.appendChild(btn);
      });
      document.getElementById("feedback").innerText = "";
      document.getElementById("passar").classList.add("hidden");
      document.getElementById("explicacaoBtn").classList.add("hidden");
      document.getElementById("textoExplicacao").classList.add("hidden");
    }

    function verificarResposta(i) {
      let q = fases[faseAtual][perguntaAtual];
      let feedback = document.getElementById("feedback");
      if (i === q.correta) {
        feedback.innerText = "✅ Correto!";
        feedback.style.color = "green";
        pontos += 10;
        document.getElementById("pontos").innerText = pontos;
        perguntaAtual++;
        if (perguntaAtual < fases[faseAtual].length) {
          setTimeout(carregarPergunta, 1000);
        } else {
          feedback.innerText = "🎉 Fase concluída!";
          document.getElementById("passar").innerText = "⬅️ Voltar ao mapa";
          document.getElementById("passar").classList.remove("hidden");
        }
      } else {
        feedback.innerText = "❌ Errado!";
        feedback.style.color = "red";
        document.getElementById("explicacaoBtn").classList.remove("hidden");
        document.getElementById("passar").classList.remove("hidden");
      }
    }

    function mostrarExplicacao() {
      let q = fases[faseAtual][perguntaAtual];
      let texto = document.getElementById("textoExplicacao");
      texto.innerText = q.explicacao;
      texto.classList.remove("hidden");
    }

    function proximaPergunta() {
      if (perguntaAtual >= fases[faseAtual].length) {
        document.getElementById("quiz").classList.add("hidden");
        document.getElementById("mapa").classList.remove("hidden");
      } else {
        carregarPergunta();
      }
    }
  </script>
</body>
</html>
