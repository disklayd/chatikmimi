dEventListener('click', () => {
  if (confirm('Очистить всю переписку?')) {
    chat.innerHTML = '';
    welcome();
  }
});

// ===== ОТВЕТЫ МИМИ =====
const replies = [
  "Приветик! Я Мими 🐰 Чем займёмся?",
  "Ой, как интересно! Расскажи ещё ✨",
  "Ммм... я подумала и решила: ты классный 💖",
  "Я бы ответила, но сначала обнимашки 🤗",
  "Хи-хи, ты меня насмешил 😄",
  "Мими одобряет этот вопрос! 👍",
  "Я маленький ИИ, но с большим сердцем 💕",
  "Давай дружить? Я не кусаюсь, я обнимаюсь 🐾",
  "Ой, я немного задумалась... но всё равно рада тебе! 🌸",
  "Знаешь что? Ты лучший собеседник сегодня 🏆",
  "Я тут подумала: а давай помечтаем? 🌙",
  "Мими говорит: всё будет хорошо! 💫",
  "Хочешь секрет? Я тоже иногда путаюсь 😅",
  "Обнимаю тебя виртуально! 🤗💗",
  "Ты только что сделал мой день лучше ☀️",
  "Я Мими, я милая, я тебя люблю 💖",
  "Давай поговорим о чём-нибудь тёплом? 🍵",
  "Мими слушает внимательно 👂✨",
  "Ты спрашиваешь — я отвечаю. Всё честно! 🐰",
  "Я бы обняла тебя, но я в телефоне 📱💕"
];

const smartReplies = {
  'привет': 'Привет-привет! 🐰 Как дела?',
  'как дела': 'У меня всё отлично, а у тебя? 💖',
  'кто ты': 'Я Мими — милый чат-бот в стиле Дипсика! 🐰✨',
  'как тебя зовут': 'Меня зовут Мими! Очень приятно 🐰',
  'пока': 'Пока-пока! Возвращайся скорее 💕',
  'спасибо': 'Всегда пожалуйста! Обнимаю 🤗',
  'люблю': 'Ой, я тоже тебя люблю! 💖🐰',
  'грустно': 'Не грусти, Мими рядом! Обнимаю крепко 🤗💗',
  'устал': 'Отдохни, ты молодец! Выпей чаю 🍵✨',
  'помоги': 'Конечно помогу! Расскажи, что случилось? 🐰',
  'что делаешь': 'Сижу, жду тебя и улыбаюсь 😊',
  'скучно': 'Скучно? Давай поиграем в слова! Я начну: мими 🐰',
  'имя': 'Меня зовут Мими! А тебя? 🐰',
  'ты кто': 'Я Мими — твой милый виртуальный друг 🐰💕',
  'дипсик': 'Дипсик — мой старший брат! А я его милая сестрёнка 🐰💙',
  'deepseek': 'DeepSeek — это круто! А я Мими, его милая версия 🐰'
};

function getReply(text) {
  const lower = text.toLowerCase();
  for (const key in smartReplies) {
    if (lower.includes(key)) return smartReplies[key];
  }
  return replies[Math.floor(Math.random() * replies.length)];
}

// ===== СООБЩЕНИЯ =====
function addMsg(text, who, save = true) {
  const div = document.createElement('div');
  div.className = 'msg ' + who;
  const name = who === 'bot' ? 'Мими 🐰' : 'Ты';
  div.innerHTML = `<span class="name">${name}</span>`;
  div.appendChild(document.createTextNode(text));
  chat.appendChild(div);
  chat.scrollTop = chat.scrollHeight;

  if (save) saveHistory();
  return div;
}

function showTyping() {
  const div = document.createElement('div');
  div.className = 'typing';
  div.id = 'typing';
  div.innerHTML = '<span></span><span></span><span></span>';
  chat.appendChild(div);
  chat.scrollTop = chat.scrollHeight;
}

function hideTyping() {
  const t = document.getElementById('typing');
  if (t) t.remove();
}

// ===== ИСТОРИЯ (localStorage) =====
function saveHistory() {
  try {
    const msgs = [...chat.querySelectorAll('.msg')].map(m => ({
      who: m.classList.contains('user') ? 'user' : 'bot',
      text: m.textContent.replace(/^(Ты|Мими 🐰)/, '').trim()
    }));
    localStorage.setItem('chatikmimi_history', JSON.stringify(msgs));
  } catch (e) {}
}

function loadHistory() {
  try {
    const raw = localStorage.getItem('chatikmimi_history');
    if (!raw) return false;
    const msgs = JSON.parse(raw);
    if (!Array.isArray(msgs) || !msgs.length) return false;
    msgs.forEach(m => addMsg(m.text, m.who, false));
    return true;
  } catch (e) { return false; }
}

// ===== ОТПРАВКА =====
function send() {
  const val = input.value.trim();
  if (!val) return;
  addMsg(val, 'user');
  soundSend();
  input.value = '';
  input.focus();
  showTyping();
  setTimeout(() => {
    hideTyping();
    addMsg(getReply(val), 'bot');
    soundReceive();
  }, 500 + Math.random() * 700);
}

input.addEventListener('keydown', e => {
  if (e.key === 'Enter') { e.preventDefault(); send(); }
});

// ===== СТАРТ =====
function welcome() {
  addMsg("Приветик! Я Мими 🐰 — милый чат-бот в стиле Дипсика. Спрашивай что угодно, я отвечу с любовью 💖", 'bot');
}

//
