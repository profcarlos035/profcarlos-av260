<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Plano Econômico - Interativo</title>
<style>
  * {
    box-sizing: border-box;
  }
  body {
    margin: 0; padding: 0;
    font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
    background: #0b0b0f;
    color: #eee;
    display: flex; flex-direction: column; min-height: 100vh;
  }
  header {
    background: linear-gradient(90deg, #6b21a8, #4338ca);
    padding: 1rem 2rem;
    font-weight: bold;
    font-size: 1.5rem;
  }
  main {
    flex: 1;
    padding: 1.5rem 2rem;
  }
  .centered {
    max-width: 400px;
    margin: 3rem auto;
    background: #1f1f2a;
    padding: 2rem;
    border-radius: 10px;
    box-shadow: 0 0 15px #5c21a8aa;
  }
  button {
    width: 100%;
    padding: 0.8rem;
    background: #9d4edd;
    border: none;
    border-radius: 6px;
    color: #fff;
    font-weight: 700;
    cursor: pointer;
    font-size: 1.1rem;
    transition: background-color 0.3s ease;
    margin-top: 1rem;
  }
  button:hover {
    background: #7b33bb;
  }
  .theme-list {
    display: flex;
    gap: 1rem;
    overflow-x: auto;
  }
  .card {
    background: #2e2e42;
    padding: 1rem;
    border-radius: 10px;
    min-width: 180px;
    cursor: pointer;
    transition: transform 0.2s ease;
  }
  .card:hover {
    transform: scale(1.05);
    box-shadow: 0 0 12px #9d4eddaa;
  }
  h2 {
    margin-bottom: 1rem;
  }
  .quiz-container {
    max-width: 480px;
    margin: auto;
    background: #2e2e42;
    padding: 1.5rem;
    border-radius: 10px;
  }
  .quiz-question {
    font-size: 1.2rem;
    margin-bottom: 1rem;
  }
  .quiz-options button {
    width: 100%;
    margin-bottom: 0.7rem;
    background: #9d4edd;
    border: none;
    border-radius: 6px;
    color: white;
    font-size: 1rem;
    cursor: pointer;
    transition: background-color 0.25s ease;
  }
  .quiz-options button:hover {
    background: #7b33bb;
  }
  footer {
    text-align: center;
    padding: 1rem;
    background: #1f1f2a;
    font-size: 0.9rem;
    color: #777;
  }
</style>
</head>
<body>

<header>Plano Econômico - Interativo</header>

<main>

<!-- Temas -->
<section id="themes-section" class="centered">
  <h2>Escolha um tema para aprender</h2>
  <div class="theme-list" id="theme-list">
    <!-- Cards gerados por JS -->
  </div>
</section>

<!-- Quiz -->
<section id="quiz-section" class="centered hidden">
  <h2 id="quiz-title">Quiz</h2>
  <div class="quiz-container">
    <p class="quiz-question" id="quiz-question"></p>
    <div class="quiz-options" id="quiz-options"></div>
  </div>
  <button id="quiz-back-btn">← Voltar aos temas</button>
</section>

</main>

<footer>© 2025 Plano Econômico Educativo - Versão Simples</footer>

<script>
  // Temas e quizzes
  const temas = [
    { id: 1, titulo: "Selic", descricao: "Entenda a taxa básica de juros." },
    { id: 2, titulo: "Inflação", descricao: "Como o IPCA afeta o poder de compra." },
    { id: 3, titulo: "Câmbio", descricao: "Impacto do dólar na economia brasileira." },
  ];

  const quizzes = {
    1: [
      {
        pergunta: "O que é a taxa Selic?",
        opcoes: [
          "Taxa básica de juros da economia",
          "Imposto sobre produtos importados",
          "Índice de inflação anual",
        ],
        respostaCorreta: 0,
      },
      {
        pergunta: "Quem define a taxa Selic?",
        opcoes: [
          "Banco Central do Brasil",
          "Ministério da Fazenda",
          "Câmara dos Deputados",
        ],
        respostaCorreta: 0,
      },
    ],
    2: [
      {
        pergunta: "O que o IPCA mede?",
        opcoes: [
          "Crescimento do PIB",
          "Inflação oficial do Brasil",
          "Taxa de desemprego",
        ],
        respostaCorreta: 1,
      },
      {
        pergunta: "Quando a inflação está alta, o que acontece com os preços?",
        opcoes: [
          "Preços sobem",
          "Preços caem",
          "Preços ficam estáveis",
        ],
        respostaCorreta: 0,
      },
    ],
    3: [
      {
        pergunta: "Como a variação do dólar afeta o Brasil?",
        opcoes: [
          "Afeta importação e exportação",
          "Define o salário mínimo",
          "Determina o valor do IPCA",
        ],
        respostaCorreta: 0,
      },
      {
        pergunta: "O que significa desvalorização do real?",
        opcoes: [
          "O real perde valor frente ao dólar",
          "O real aumenta valor frente ao dólar",
          "O dólar perde valor frente ao real",
        ],
        respostaCorreta: 0,
      },
    ],
  };

  // Elementos DOM
  const themesSection = document.getElementById("themes-section");
  const quizSection = document.getElementById("quiz-section");

  const themeListDiv = document.getElementById("theme-list");
  const quizTitle = document.getElementById("quiz-title");
  const quizQuestion = document.getElementById("quiz-question");
  const quizOptionsDiv = document.getElementById("quiz-options");

  const quizBackBtn = document.getElementById("quiz-back-btn");

  // Estado atual
  let currentTema = null;
  let currentQuizIndex = 0;
  let respostas = [];

  // Funções para mostrar seções
  function showSection(sectionToShow) {
    themesSection.classList.add("hidden");
    quizSection.classList.add("hidden");
    sectionToShow.classList.remove("hidden");
  }

  // Renderizar cards de temas
  function renderThemes() {
    themeListDiv.innerHTML = "";
    temas.forEach((tema) => {
      const card = document.createElement("div");
      card.className = "card";
      card.innerHTML = `
        <h3>${tema.titulo}</h3>
        <p>${tema.descricao}</p>
      `;
      card.onclick = () => {
        startQuiz(tema.id);
      };
      themeListDiv.appendChild(card);
    });
  }

  // Iniciar quiz para tema selecionado
  function startQuiz(temaId) {
    currentTema = temaId;
    currentQuizIndex = 0;
    respostas = [];
    showSection(quizSection);
    showQuizQuestion();
  }

  // Mostrar pergunta atual
  function showQuizQuestion() {
    const quizList = quizzes[currentTema];
    if (currentQuizIndex >= quizList.length) {
      showQuizResults();
      return;
    }

    const q = quizList[currentQuizIndex];
    quizTitle.textContent = `Quiz: ${temas.find(t => t.id === currentTema).titulo}`;
    quizQuestion.textContent = q.pergunta;
    quizOptionsDiv.innerHTML = "";

    q.opcoes.forEach((opcao, idx) => {
      const btn = document.createElement("button");
      btn.textContent = opcao;
      btn.onclick = () => {
        respostas.push(idx);
        currentQuizIndex++;
        showQuizQuestion();
      };
      quizOptionsDiv.appendChild(btn);
    });
  }

  // Mostrar resultado final
  function showQuizResults() {
    const quizList = quizzes[currentTema];
    const correctCount = respostas.filter((r, i) => r === quizList[i].respostaCorreta).length;
    quizTitle.textContent = `Quiz: ${temas.find(t => t.id === currentTema).titulo} — Resultado`;
    quizQuestion.textContent = `Você acertou ${correctCount} de ${quizList.length} perguntas!`;
    quizOptionsDiv.innerHTML = "";
  }

  // Voltar para lista de temas
  quizBackBtn.onclick = () => {
    renderThemes();
    showSection(themesSection);
  };

  // Inicializar app
  renderThemes();
  showSection(themesSection);
</script>

</body>
</html>
