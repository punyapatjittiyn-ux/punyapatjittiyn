<!DOCTYPE html>
<html lang="th">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>เกมทายเทศกาล</title>

  <style>
    body {
      font-family: Arial, sans-serif;
      background: linear-gradient(135deg, #fff7f7, #e8ecf3);
      margin: 0;
      padding: 0;
      text-align: center;
    }

    .quiz-container {
      margin: auto;
      margin-top: 20px;
      width: 90%;
      max-width: 600px;
      background: white;
      padding: 20px;
      border-radius: 15px;
      box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.2);
    }

    .festival-image {
      width: 100%;
      max-height: 250px;
      object-fit: cover;
      border-radius: 12px;
      margin-bottom: 15px;
    }

    .timer {
      font-size: 24px;
      color: red;
      margin-bottom: 10px;
    }

    .clue {
      font-size: 20px;
      margin: 15px 0;
    }

    .options {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      gap: 10px;
    }

    .option-btn {
      padding: 10px 20px;
      border: 2px solid #3498db;
      background: white;
      border-radius: 8px;
      cursor: pointer;
    }

    .option-btn:hover {
      background: #3498db;
      color: white;
    }

    .voice-btn {
      margin-top: 15px;
      padding: 10px 20px;
      background: #ff9800;
      color: white;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      font-size: 18px;
    }

    .result {
      font-size: 22px;
      display: none;
      margin-top: 20px;
    }

    .restart-btn {
      margin-top: 10px;
      padding: 10px 20px;
      background: #2ecc71;
      color: white;
      border-radius: 8px;
      cursor: pointer;
      display: none;
    }
  </style>
</head>

<body>

  <audio id="bgm" loop>
    <source src="audio/music.mp3" type="audio/mp3">
  </audio>

  <div class="quiz-container">
    <img id="festivalImg" class="festival-image" src="" alt="festival">

    <div class="timer">เวลาเหลือ: <span id="time">30</span> วินาที</div>

    <div class="clue" id="clue">กำลังโหลดคำใบ...</div>

    <div class="options" id="options"></div>

    <button class="voice-btn" id="voiceBtn">🎤 ตอบด้วยเสียง</button>

    <div class="result" id="result">คะแนน: <span id="score">0</span></div>
    <button class="restart-btn" id="restart">เล่นใหม่</button>
  </div>

<script src="game.js"></script>
</body>
</html>
/* game.js
  1) วางไฟล์นี้ในเดียวกับ index.html
  2) สร้างโฟลเดอร์ images/ และ audio/
     - images/<filename>.jpg (ชื่อไฟล์ตาม quizData[].img)
     - audio/music.mp3  (background)
     - audio/success.mp3 (optional)
     - audio/fail.mp3 (optional)
  3) เปิดบน GitHub Pages แล้วทดสอบ (บางเบราว์เซอร์อาจขออนุญาตเล่นเสียง/ใช้ไมค์)
*/

/* ================== ปรับค่าได้ ================== */
const TIMER_SECONDS = 30;
const MUSIC_SRC = "audio/music.mp3";
const SUCCESS_SRC = "audio/success.mp3"; // optional
const FAIL_SRC = "audio/fail.mp3"; // optional
/* ================================================ */

const quizData = [
  { festival: "วันปีใหม่", clue: "พลุ", img: "images/newyear.jpg" },
  { festival: "สงกรานต์", clue: "สาดน้ำ", img: "images/songkran.jpg" },
  { festival: "ฮาโลวีน", clue: "ฟักทอง", img: "images/halloween.jpg" },
  { festival: "คริสต์มาส", clue: "ต้นคริสต์มาส", img: "images/christmas.jpg" },
  { festival: "ลอยกระทง", clue: "กระทง", img: "images/loykrathong.jpg" },
  { festival: "ตรุษจีน", clue: "อั่งเปา", img: "images/chinese_newyear.jpg" },
  { festival: "วาเลนไทน์", clue: "หัวใจ", img: "images/valentine.jpg" },
  { festival: "วันเด็กแห่งชาติ", clue: "ของเล่น", img: "images/children.jpg" },
  { festival: "วันแม่", clue: "ดอกมะลิ", img: "images/mother.jpg" },
  { festival: "วันพ่อ", clue: "ผ้าไหม", img: "images/father.jpg" },
  { festival: "วันมาฆบูชา", clue: "ดอกบัว", img: "images/makha.jpg" },
  { festival: "วันวิสาขบูชา", clue: "ธูปเทียน", img: "images/visakha.jpg" },
  { festival: "งานวัด", clue: "ชิงช้า", img: "images/fair.jpg" },
  { festival: "เทศกาลบอลลูน", clue: "ลูกโป่งร้อน", img: "images/balloon.jpg" },
  { festival: "เทศกาลอาหาร", clue: "ซุ้มของกิน", img: "images/foodfest.jpg" },
  { festival: "เทศกาลภาพยนตร์", clue: "เรดคาร์เพท", img: "images/filmfest.jpg" },
  { festival: "เทศกาลหนังสือ", clue: "หนังสือ", img: "images/bookfest.jpg" },
  { festival: "วันกินเจ", clue: "เจ", img: "images/vegetarian.jpg" },
  { festival: "โอบอน", clue: "โคมไฟ", img: "images/obon.jpg" },
  { festival: "เทศกาลดนตรี", clue: "เสียงกีตาร์", img: "images/musicfest.jpg" }
];

// --- DOM elements
const timeEl = document.getElementById("time");
const clueEl = document.getElementById("clue");
const optionsEl = document.getElementById("options");
const festivalImg = document.getElementById("festivalImg");
const voiceBtn = document.getElementById("voiceBtn");
const bgm = document.getElementById("bgm");
const resultEl = document.getElementById("result");
const scoreEl = document.getElementById("score");
const restartBtn = document.getElementById("restart");

let currentIndex = 0;
let score = 0;
let timeLeft = TIMER_SECONDS;
let timerInterval = null;

// Audio feedback
const successAudio = new Audio(SUCCESS_SRC);
const failAudio = new Audio(FAIL_SRC);

// --- Speech recognition setup (Web Speech API)
const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition || null;
let recognizer = null;
let recognizing = false;

if (SpeechRecognition) {
  recognizer = new SpeechRecognition();
  recognizer.lang = "th-TH"; // Thai
  recognizer.interimResults = false;
  recognizer.maxAlternatives = 1;

  recognizer.onresult = (e) => {
    const transcript = e.results[0][0].transcript;
    // console.log("Recognized:", transcript);
    handleSpokenAnswer(transcript);
  };

  recognizer.onend = () => {
    recognizing = false;
    updateVoiceBtn();
  };

  recognizer.onerror = (e) => {
    recognizing = false;
    updateVoiceBtn();
    console.warn("Speech recognition error", e);
    alert("ไม่สามารถใช้การฟังคำตอบได้ (Speech recognition error). โปรดลองอีกครั้งหรือพิมพ์เลือกคำตอบแทน");
  };
} else {
  voiceBtn.style.display = "none"; // ซ่อนปุ่มถ้าไม่รองรับ
}

// ======= Helpers =======
function normalizeThai(s) {
  if (!s) return "";
  // lowercase, remove spaces and punctuation for easier matching
  return s.toLowerCase().replace(/\s+/g, "").replace(/[.,\/#!$%\^&\*;:{}=\-_`~()\"\'$begin:math:display$$end:math:display$]/g, "");
}

function updateVoiceBtn() {
  if (!recognizer) return;
  voiceBtn.textContent = recognizing ? "⏸ หยุดฟัง" : "🎤 ตอบด้วยเสียง";
}

// ======= Timer =======
function startTimer() {
  clearInterval(timerInterval);
  timeLeft = TIMER_SECONDS;
  timeEl.textContent = timeLeft;
  timerInterval = setInterval(() => {
    timeLeft--;
    timeEl.textContent = timeLeft;
    if (timeLeft <= 0) {
      clearInterval(timerInterval);
      playFail(); // optional failure sound
      moveToNext();
    }
  }, 1000);
}

function stopTimer() {
  clearInterval(timerInterval);
}

// ======= Load question =======
function loadQuestion() {
  if (currentIndex >= quizData.length) {
    endQuiz();
    return;
  }

  const q = quizData[currentIndex];
  // show image (fallback to placeholder if not found)
  festivalImg.src = q.img || "";
  festivalImg.alt = q.festival;

  // show clue
  clueEl.textContent = q.clue;

  // build choices (correct + 3 random)
  optionsEl.innerHTML = "";
  const correct = q.festival;
  let choices = [correct];
  while (choices.length < 4) {
    const rand = quizData[Math.floor(Math.random() * quizData.length)].festival;
    if (!choices.includes(rand)) choices.push(rand);
  }
  choices.sort(() => Math.random() - 0.5);

  for (const c of choices) {
    const btn = document.createElement("button");
    btn.className = "option-btn";
    btn.textContent = c;
    btn.onclick = () => {
      checkAnswer(c);
    };
    optionsEl.appendChild(btn);
  }

  // restart timer
  startTimer();
  // ensure music tries to play (user interaction might be required in browser)
  tryPlayMusic();
  // reset voice UI
  updateVoiceBtn();
}

// ======= Answer checking =======
function checkAnswer(selected) {
  stopTimer();
  const correct = quizData[currentIndex].festival;
  if (selected === correct) {
    score++;
    playSuccess();
  } else {
    playFail();
  }
  currentIndex++;
  // small delay to allow sound/feedback
  setTimeout(() => loadQuestion(), 400);
}

function handleSpokenAnswer(transcript) {
  if (!transcript) return;
  // normalize
  const spoken = normalizeThai(transcript);
  // Try to match to festival names:
  const q = quizData[currentIndex];
  const correctNorm = normalizeThai(q.festival);

  // If spoken contains festival name substring OR vice versa -> accept
  if (spoken.includes(correctNorm) || correctNorm.includes(spoken)) {
    // correct
    stopTimer();
    score++;
    playSuccess();
    currentIndex++;
    setTimeout(() => loadQuestion(), 400);
    return;
  }

  // Otherwise, try to find any festival that matches spoken text (user might say full festival)
  let matchedIndex = -1;
  for (let i = 0; i < quizData.length; i++) {
    const fNorm = normalizeThai(quizData[i].festival);
    if (spoken.includes(fNorm) || fNorm.includes(spoken)) {
      matchedIndex = i;
      break;
    }
  }
  if (matchedIndex !== -1) {
    // if matched festival equals correct one?
    if (normalizeThai(quizData[matchedIndex].festival) === correctNorm) {
      stopTimer();
      score++;
      playSuccess();
    } else {
      playFail();
    }
    currentIndex++;
    setTimeout(() => loadQuestion(), 400);
    return;
  }

  // If not matched - treat as wrong and move on
  playFail();
  stopTimer();
  currentIndex++;
  setTimeout(() => loadQuestion(), 400);
}

// ======= Move to next on timeout =======
function moveToNext() {
  // show the correct answer briefly (optional)
  // e.g., flash correct button (we can highlight if present)
  highlightCorrectThenNext();
}

function highlightCorrectThenNext() {
  const correct = quizData[currentIndex].festival;
  // try to find button and highlight
  const buttons = Array.from(optionsEl.querySelectorAll("button"));
  for (const b of buttons) {
    if (b.textContent === correct) {
      b.style.borderColor = "#2ecc71";
      b.style.background = "#2ecc71";
      b.style.color = "white";
    } else {
      b.style.opacity = "0.6";
    }
  }
  setTimeout(() => {
    // reset styles
    for (const b of buttons) {
      b.style = "";
      b.className = "option-btn";
    }
    currentIndex++;
    loadQuestion();
  }, 800);
}

// ======= End quiz =======
function endQuiz() {
  stopTimer();
  // hide UI pieces & show result
  document.querySelector(".timer").style.display = "none";
  clueEl.style.display = "none";
  optionsEl.style.display = "none";
  festivalImg.style.display = "none";
  voiceBtn.style.display = "none";
  resultEl.style.display = "block";
  scoreEl.textContent = score + " / " + quizData.length;
  restartBtn.style.display = "inline-block";
  // stop music
  try { bgm.pause(); bgm.currentTime = 0; } catch (e) {}
}

// ======= Restart =======
restartBtn.onclick = () => {
  currentIndex = 0;
  score = 0;
  resultEl.style.display = "none";
  restartBtn.style.display = "none";
  document.querySelector(".timer").style.display = "";
  clueEl.style.display = "";
  optionsEl.style.display = "flex";
  festivalImg.style.display = "";
  if (SpeechRecognition) voiceBtn.style.display = "";
  loadQuestion();
};

// ======= Music control & play attempts =======
function tryPlayMusic() {
  if (!bgm) return;
  // attempt to play; many browsers require user gesture — if blocked, user can press a Play button (we'll use the voiceBtn as a gesture)
  bgm.src = MUSIC_SRC;
  bgm.loop = true;
  bgm.volume = 0.5;
  bgm.play().catch((err) => {
    // console.log("Autoplay prevented: user interaction required", err);
    // do nothing — music can be started by user clicking anywhere (e.g., voiceBtn)
  });
}

// play success/fail safely
function playSuccess() {
  if (SUCCESS_SRC) {
    successAudio.currentTime = 0;
    successAudio.play().catch(()=>{});
  }
}
function playFail() {
  if (FAIL_SRC) {
    failAudio.currentTime = 0;
    failAudio.play().catch(()=>{});
  }
}

// ======= Voice button behavior =======
voiceBtn.addEventListener("click", () => {
  // also treat click as a user gesture to start music if not started
  tryPlayMusic();

  if (!recognizer) {
    alert("เบราว์เซอร์ของคุณไม่รองรับการฟังคำตอบ (Speech Recognition). กรุณาตอบด้วยการกดปุ่มแทน");
    return;
  }

  if (!recognizing) {
    try {
      recognizer.start();
      recognizing = true;
      updateVoiceBtn();
    } catch (e) {
      console.warn("recognizer.start() error", e);
    }
  } else {
    try {
      recognizer.stop();
      recognizing = false;
      updateVoiceBtn();
    } catch (e) {
      console.warn("recognizer.stop() error", e);
    }
  }
});

// ======= Preload images for smoother display =======
function preloadImages(list, callback) {
  let loaded = 0;
  if (!list.length) return callback && callback();
  for (const src of list) {
    const img = new Image();
    img.onload = img.onerror = () => {
      loaded++;
      if (loaded >= list.length && callback) callback();
    };
    img.src = src;
  }
}

// collect all images
const imgList = quizData.map(q => q.img).filter(Boolean);
preloadImages(imgList, () => {
  // Start the game when images loaded (or even if some fail)
  loadQuestion();
});

// If images empty, just start immediately
if (!imgList.length) loadQuestion();
