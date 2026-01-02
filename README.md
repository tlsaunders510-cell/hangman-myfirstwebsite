

<!doctype HTML> 
<html lang="en">
<head>
  <meta charset="utf-8" />
  <title>Hangman Game</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    :root {
      color-scheme: dark;
      --bg: #020617;
      --card: #020617;
      --accent: #38bdf8;
      --accent-soft: rgba(56, 189, 248, 0.12);
      --text: #e5e7eb;
      --muted: #9ca3af;
      --error: #f97373;
    }

    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      min-height: 100vh;
      display: flex;
      align-items: center;
      justify-content: center;
      padding: 16px;
      background:
        radial-gradient(circle at top, #1f2937 0, #020617 50%, #000 100%);
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI",
        Roboto, sans-serif;
      color: var(--text);
    }

    .game {
      width: 100%;
      max-width: 640px;
      background: radial-gradient(circle at top left, #020617 0, #020617 70%);
      border-radius: 24px;
      padding: 24px 20px 20px;
      box-shadow:
        0 18px 60px rgba(0, 0, 0, 0.75),
        0 0 0 1px rgba(148, 163, 184, 0.08);
      border: 1px solid rgba(148, 163, 184, 0.25);
    }

    .game-header {
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 12px;
      margin-bottom: 20px;
    }

    h1 {
      margin: 0;
      font-size: 1.25rem;
      letter-spacing: 0.12em;
      text-transform: uppercase;
      color: var(--muted);
    }

    .lives {
      padding: 6px 10px;
      border-radius: 999px;
      border: 1px solid rgba(148, 163, 184, 0.35);
      font-size: 0.8rem;
      text-transform: uppercase;
      letter-spacing: 0.1em;
      color: var(--muted);
      display: inline-flex;
      align-items: center;
      gap: 6px;
      background: rgba(15, 23, 42, 0.8);
      backdrop-filter: blur(4px);
    }

    .lives-dot {
      width: 6px;
      height: 6px;
      border-radius: 999px;
      background: var(--accent);
      box-shadow: 0 0 8px rgba(56, 189, 248, 0.9);
    }

    .board {
      display: grid;
      grid-template-columns: minmax(0, 1.3fr) minmax(0, 1.1fr);
      gap: 20px;
      align-items: stretch;
    }

    @media (max-width: 640px) {
      .board {
        grid-template-columns: minmax(0, 1fr);
      }
    }

    /* Hangman visual */
    .hangman {
      border-radius: 18px;
      padding: 14px 16px 16px;
      background: radial-gradient(circle at top, #020617 0, #020617 80%);
      border: 1px solid rgba(55, 65, 81, 0.8);
      position: relative;
      overflow: hidden;
    }

    .gallows {
      height: 180px;
      position: relative;
    }

    .gallows-base,
    .gallows-pole,
    .gallows-top,
    .gallows-rope {
      position: absolute;
      background: linear-gradient(180deg, #4b5563, #020617);
      border-radius: 999px;
    }

    .gallows-base {
      bottom: 8px;
      left: 16px;
      right: 16px;
      height: 6px;
    }

    .gallows-pole {
      left: 36px;
      bottom: 14px;
      width: 6px;
      height: 130px;
    }

    .gallows-top {
      left: 38px;
      top: 16px;
      height: 6px;
      width: 80px;
    }

    .gallows-rope {
      left: 110px;
      top: 22px;
      width: 2px;
      height: 24px;
      background: linear-gradient(180deg, #e5e7eb, #6b7280);
    }

    .figure-part {
      position: absolute;
      left: 110px;
      transform: translateX(-50%);
      opacity: 0;
      transition: opacity 0.2s ease-out;
    }

    .figure-part.visible {
      opacity: 1;
    }

    .head {
      top: 46px;
      width: 32px;
      height: 32px;
      border-radius: 999px;
      border: 3px solid #e5e7eb;
      box-shadow: 0 0 12px rgba(148, 163, 184, 0.5);
    }

    .body {
      top: 76px;
      width: 3px;
      height: 46px;
      background: #e5e7eb;
    }

    .arm-left,
    .arm-right,
    .leg-left,
    .leg-right {
      width: 3px;
      height: 26px;
      background: #e5e7eb;
      transform-origin: top;
    }

    .arm-left {
      top: 82px;
      left: 102px;
      transform: rotate(-30deg);
    }

    .arm-right {
      top: 82px;
      left: 118px;
      transform: rotate(30deg);
    }

    .leg-left {
      top: 120px;
      left: 104px;
      transform: rotate(-22deg);
    }

    .leg-right {
      top: 120px;
      left: 116px;
      transform: rotate(22deg);
    }

    .hint {
      margin-top: 8px;
      font-size: 0.8rem;
      color: var(--muted);
    }

    /* Word display */
    .word-wrapper {
      border-radius: 18px;
      padding: 14px 14px 16px;
      background: radial-gradient(circle at top, #020617 0, #020617 80%);
      border: 1px solid rgba(148, 163, 184, 0.4);
      display: flex;
      flex-direction: column;
      gap: 10px;
      min-height: 90px;
      justify-content: space-between;
    }

    .word {
      display: flex;
      gap: 8px;
      flex-wrap: wrap;
      font-size: 1.6rem;
      letter-spacing: 0.1em;
    }

    .letter-box {
      min-width: 22px;
      text-align: center;
      border-bottom: 2px solid rgba(148, 163, 184, 0.7);
      padding-bottom: 4px;
      text-transform: uppercase;
    }

    .letter-box.revealed {
      color: var(--accent);
      text-shadow: 0 0 8px rgba(56, 189, 248, 0.9);
    }

    .status {
      font-size: 0.85rem;
      color: var(--muted);
    }

    .status strong {
      color: var(--accent);
    }

    /* Keyboard */
    .keyboard {
      margin-top: 16px;
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(28px, 1fr));
      gap: 6px;
    }

    .key {
      padding: 6px 0;
      border-radius: 999px;
      border: 1px solid rgba(75, 85, 99, 0.9);
      background: radial-gradient(circle at top, #111827 0, #020617 80%);
      font-size: 0.8rem;
      text-transform: uppercase;
      cursor: pointer;
      color: var(--text);
      transition: transform 0.08s ease-out, box-shadow 0.08s ease-out,
        border-color 0.1s ease-out, background 0.1s ease-out;
      user-select: none;
    }

    .key:hover:not(:disabled) {
      transform: translateY(-1px);
      box-shadow: 0 6px 14px rgba(15, 23, 42, 0.8);
      border-color: rgba(148, 163, 184, 1);
    }

    .key:active:not(:disabled) {
      transform: translateY(0);
      box-shadow: none;
    }

    .key.correct {
      background: radial-gradient(circle at top, rgba(22, 163, 74, 0.4), #064e3b);
      border-color: #22c55e;
    }

    .key.wrong {
      background: radial-gradient(circle at top, rgba(127, 29, 29, 0.6), #111827);
      border-color: rgba(248, 113, 113, 0.9);
      color: #fecaca;
    }

    .key:disabled {
      opacity: 0.85;
      cursor: default;
    }

    .footer {
      margin-top: 16px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      gap: 8px;
      flex-wrap: wrap;
      font-size: 0.8rem;
      color: var(--muted);
    }

    .btn {
      padding: 7px 14px;
      border-radius: 999px;
      border: 1px solid rgba(148, 163, 184, 0.9);
      background: radial-gradient(circle at top, var(--accent-soft), #020617);
      color: var(--text);
      font-size: 0.8rem;
      text-transform: uppercase;
      letter-spacing: 0.1em;
      cursor: pointer;
      display: inline-flex;
      align-items: center;
      gap: 6px;
      transition: transform 0.1s ease-out, box-shadow 0.1s ease-out,
        border-color 0.1s ease-out, background 0.1s ease-out;
      white-space: nowrap;
    }

    .btn:hover {
      transform: translateY(-1px);
      box-shadow: 0 10px 22px rgba(15, 23, 42, 0.85);
      border-color: var(--accent);
    }

    .btn:active {
      transform: translateY(0);
      box-shadow: none;
    }

    .message {
      font-size: 0.9rem;
      color: var(--muted);
    }

    .message.win {
      color: #4ade80;
    }

    .message.lose {
      color: var(--error);
    }

    .answer {
      font-weight: 600;
      color: #e5e7eb;
      text-transform: uppercase;
    }
  </style>
</head>
<body>
  <main class="game">
    <div class="game-header">
      <h1>Hangman</h1>
      <div class="lives">
        <span class="lives-dot"></span>
        <span id="lives-text">Lives: 6</span>
      </div>
    </div>

    <div class="board">
      <section class="hangman" aria-hidden="true">
        <div class="gallows">
          <div class="gallows-base"></div>
          <div class="gallows-pole"></div>
          <div class="gallows-top"></div>
          <div class="gallows-rope"></div>

          <div class="figure-part head"></div>
          <div class="figure-part body"></div>
          <div class="figure-part arm-left"></div>
          <div class="figure-part arm-right"></div>
          <div class="figure-part leg-left"></div>
          <div class="figure-part leg-right"></div>
        </div>
        <p class="hint" id="hint-text"></p>
      </section>

      <section>
        <div class="word-wrapper">
          <div class="word" id="word"></div>
          <div class="status" id="status"></div>
        </div>
        <div class="keyboard" id="keyboard"></div>
      </section>
    </div>

    <div class="footer">
      <button class="btn" id="new-game-btn">
        <span>New word</span>
      </button>
      <div class="message" id="message"></div>
    </div>
  </main>

  <script>
  // WORD DATABASE – this will be loaded from words.json
  let WORDS = [];

  const MAX_WRONG_GUESSES = 6;

  const wordEl = document.getElementById("word");
  const statusEl = document.getElementById("status");
  const keyboardEl = document.getElementById("keyboard");
  const messageEl = document.getElementById("message");
  const livesTextEl = document.getElementById("lives-text");
  const hintTextEl = document.getElementById("hint-text");
  const newGameBtn = document.getElementById("new-game-btn");
  const figureParts = Array.from(
    document.querySelectorAll(".figure-part")
  );

  let currentWord = "";
  let currentHint = "";
  let correctLetters = new Set();
  let guessedLetters = new Set();
  let wrongGuesses = 0;
  let gameOver = false;

  // 🔹 Load words from words.json
  async function loadWords() {
    try {
      const response = await fetch("words.json");
      if (!response.ok) {
        throw new Error("Failed to load words.json");
      }
      const data = await response.json();

      if (!Array.isArray(data) || data.length === 0) {
        throw new Error("words.json is empty or invalid");
      }

      WORDS = data;
      console.log(`Loaded ${WORDS.length} words.`);
      startNewGame();
    } catch (error) {
      console.error(error);
      messageEl.textContent = "Error loading word list. Using fallback words.";
      messageEl.className = "message lose";

      // Fallback minimal list if something goes wrong
      WORDS = [
        { word: "fallback", hint: "Fallback word list." },
        { word: "error", hint: "Something went wrong loading words." }
      ];
      startNewGame();
    }
  }

  function pickRandomWord() {
    const random = WORDS[Math.floor(Math.random() * WORDS.length)];
    return random;
  }

  function setupKeyboard() {
    keyboardEl.innerHTML = "";
    const letters = "abcdefghijklmnopqrstuvwxyz".split("");
    letters.forEach((letter) => {
      const btn = document.createElement("button");
      btn.textContent = letter;
      btn.className = "key";
      btn.addEventListener("click", () => handleGuess(letter, btn));
      keyboardEl.appendChild(btn);
    });
  }

  function updateWordDisplay() {
    wordEl.innerHTML = "";
    currentWord.split("").forEach((letter) => {
      const span = document.createElement("span");
      span.className = "letter-box";
      if (letter === " ") {
        span.textContent = " ";
        span.style.borderBottom = "none";
      } else if (correctLetters.has(letter)) {
        span.textContent = letter;
        span.classList.add("revealed");
      } else {
        span.textContent = "";
      }
      wordEl.appendChild(span);
    });
  }

  function updateStatus() {
    statusEl.innerHTML = `
      Wrong guesses: <strong>${wrongGuesses}</strong> / ${MAX_WRONG_GUESSES}
    `;
    livesTextEl.textContent = `Lives: ${MAX_WRONG_GUESSES - wrongGuesses}`;
    figureParts.forEach((part, index) => {
      if (index < wrongGuesses) {
        part.classList.add("visible");
      } else {
        part.classList.remove("visible");
      }
    });
  }

  function checkWin() {
    return currentWord
      .split("")
      .filter((c) => c !== " ")
      .every((c) => correctLetters.has(c));
  }

  function endGame(win) {
    gameOver = true;
    const answerHtml = `<span class="answer">${currentWord}</span>`;

    if (win) {
      messageEl.innerHTML = `You win! The word was ${answerHtml}.`;
      messageEl.className = "message win";
    } else {
      messageEl.innerHTML = `You lost. The word was ${answerHtml}.`;
      messageEl.className = "message lose";
    }

    keyboardEl.querySelectorAll("button").forEach((btn) => {
      btn.disabled = true;
    });
  }

  function handleGuess(letter, btn) {
    if (gameOver || guessedLetters.has(letter)) return;
    guessedLetters.add(letter);

    const isCorrect = currentWord.includes(letter);
    if (isCorrect) {
      correctLetters.add(letter);
      btn.classList.add("correct");
    } else {
      wrongGuesses++;
      btn.classList.add("wrong");
    }

    btn.disabled = true;
    updateWordDisplay();
    updateStatus();

    if (checkWin()) {
      endGame(true);
    } else if (wrongGuesses >= MAX_WRONG_GUESSES) {
      endGame(false);
    }
  }

  function startNewGame() {
    if (!WORDS || WORDS.length === 0) {
      messageEl.textContent = "No words available.";
      messageEl.className = "message lose";
      return;
    }

    const { word, hint } = pickRandomWord();
    currentWord = word.toLowerCase();
    currentHint = hint || "No hint available.";
    correctLetters = new Set();
    guessedLetters = new Set();
    wrongGuesses = 0;
    gameOver = false;

    hintTextEl.textContent = `Hint: ${currentHint}`;
    messageEl.textContent = "Guess the word by choosing letters.";
    messageEl.className = "message";

    setupKeyboard();
    updateWordDisplay();
    updateStatus();
  }

  newGameBtn.addEventListener("click", startNewGame);

  // 🔹 Start everything by loading the word list
  loadWords();
</script>


</body>
</html>
