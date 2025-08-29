# HTML5 Игровая Коллекция - Документация проекта

## 📋 Обзор проекта

HTML5 Игровая Коллекция — это автономная коллекция ретро-игр и утилит, созданная полностью на HTML5, CSS и JavaScript без внешних зависимостей.

### 🎯 Ключевые особенности
- **Единый лаунчер** для навигации между играми и программами
- **Автономность** — каждый файл работает независимо
- **Адаптивный дизайн** с поддержкой всех устройств
- **Сенсорное управление** для мобильных устройств
- **Единый стиль** дизайна во всех приложениях
- **Глобальные настройки** звука, темы, вибрации

### 📁 Структура проекта
```
html5/
├── index.html                    # Главный лаунчер
├── games/                        # Папка с играми
│   ├── snake_game.html          # Змейка
│   ├── tetris_game.html         # Тетрис
│   ├── pong_game.html           # Понг
│   ├── breakout_game.html       # Арканоид
│   └── flappy_bird_game.html    # Летящая Птичка
├── programs/                     # Папка с программами
│   ├── calculator.html          # Калькулятор
│   ├── color_picker.html        # Палитра цветов
│   ├── password_generator.html  # Генератор паролей
│   ├── qr_generator.html        # QR генератор
│   ├── text_editor.html         # Текстовый редактор
│   └── todo_list.html           # Список дел
└── README.md                     # Данная документация
```

## 🏗️ Архитектурные требования

### ✅ Обязательные элементы для каждого файла

#### 1. Навигация обратно к лаунчеру
```html
<div class="game-header"> <!-- или .program-header -->
    <a href="../index.html" class="back-btn" title="Вернуться к лаунчеру">←</a>
    <h1 class="game-title">🎮 Название</h1>
    <p>Описание приложения</p>
</div>
```

#### 2. CSS стили для кнопки "Назад"
```css
.back-btn {
    position: absolute;
    top: 15px;
    left: 15px;
    background: rgba(255, 255, 255, 0.2);
    border: 2px solid rgba(255, 255, 255, 0.3);
    color: white;
    width: 40px;
    height: 40px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
    text-decoration: none;
}

.back-btn:hover {
    background: rgba(255, 255, 255, 0.3);
    transform: scale(1.1);
}
```

#### 3. Обязательные CSS свойства для body
```css
body {
    margin: 0;
    padding: 0;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    font-family: Arial, sans-serif;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    min-height: 100vh;
    user-select: none;
    -webkit-user-select: none;
    -webkit-touch-callout: none;
    -webkit-tap-highlight-color: transparent;
}
```

## 📱 Требования к адаптивности

### Медиа-запросы
```css
@media (max-width: 768px) {
    .game-header, .program-header {
        padding: 15px;
    }
    
    .game-title, .program-title {
        font-size: 1.5rem;
    }
    
    .touch-controls {
        display: grid; /* Показать сенсорные элементы */
    }
}

@media (max-width: 768px) or (pointer: coarse) {
    .touch-controls {
        display: grid;
    }
}
```

### Адаптивный canvas (для игр)
```javascript
function resizeCanvas() {
    const maxWidth = Math.min(800, window.innerWidth * 0.95);
    const maxHeight = Math.min(600, window.innerHeight * 0.7);
    const ratio = 800 / 600;
    
    let width = maxWidth;
    let height = width / ratio;
    
    if (height > maxHeight) {
        height = maxHeight;
        width = height * ratio;
    }
    
    canvas.width = width;
    canvas.height = height;
    
    updateGameDimensions();
}

resizeCanvas();
window.addEventListener('resize', resizeCanvas);
```

## 🔊 Звуковая система

### Обязательная реализация Web Audio API
```javascript
const audioContext = new (window.AudioContext || window.webkitAudioContext)();

function playSound(frequency = 440, duration = 200, type = 'sine') {
    if (!gameStats.settings.sound) return;
    
    const oscillator = audioContext.createOscillator();
    const gainNode = audioContext.createGain();
    
    oscillator.connect(gainNode);
    gainNode.connect(audioContext.destination);
    
    oscillator.frequency.value = frequency;
    oscillator.type = type;
    
    gainNode.gain.setValueAtTime(0.1, audioContext.currentTime);
    gainNode.gain.exponentialRampToValueAtTime(0.01, 
        audioContext.currentTime + duration / 1000);
    
    oscillator.start(audioContext.currentTime);
    oscillator.stop(audioContext.currentTime + duration / 1000);
}

// Вибрация для мобильных
function vibrate(pattern = [100]) {
    if (!gameStats.settings.vibration) return;
    if (navigator.vibrate) {
        navigator.vibrate(pattern);
    }
}
```

## 💾 Управление данными

### Структура localStorage
```javascript
let gameStats = {
    sessionCount: 0,
    totalPlayTime: 0,
    gamesPlayed: {},
    lastPlayed: null,
    settings: {
        sound: true,
        music: true,
        vibration: true,
        darkTheme: false
    }
};

// Функции сохранения и загрузки
function loadGameData() {
    const saved = localStorage.getItem('html5GameCollection');
    if (saved) {
        gameStats = { ...gameStats, ...JSON.parse(saved) };
    }
}

function saveGameData() {
    localStorage.setItem('html5GameCollection', JSON.stringify(gameStats));
}

// Сохранение рекордов отдельных игр
function saveHighScore(gameName, score) {
    localStorage.setItem(gameName + 'HighScore', score);
}
```

## 🎮 Требования к играм

### Обработка событий
```javascript
// Обязательная поддержка всех типов ввода
document.addEventListener('keydown', handleKeyboard);
canvas.addEventListener('click', handleClick);
canvas.addEventListener('touchstart', handleTouch);
canvas.addEventListener('touchmove', handleTouchMove);

// Предотвращение нежелательного поведения браузера
document.addEventListener('contextmenu', e => e.preventDefault());

let lastTouchEnd = 0;
document.addEventListener('touchend', e => {
    const now = new Date().getTime();
    if (now - lastTouchEnd <= 300) {
        e.preventDefault();
    }
    lastTouchEnd = now;
}, false);
```

### Система счета и статистики
```javascript
let score = 0;
let highScore = getHighScore('GameName');

function updateScore(points) {
    score += points;
    if (score > highScore) {
        highScore = score;
        saveHighScore('GameName', highScore);
    }
    updateDisplay();
}

function updateGameStats() {
    gameStats.sessionCount++;
    gameStats.gamesPlayed['GameName'] = (gameStats.gamesPlayed['GameName'] || 0) + 1;
    gameStats.lastPlayed = 'GameName';
    saveGameData();
}
```

## 🛠️ Интеграция в лаунчер

### Добавление новой игры/программы

1. **Создание карточки в index.html**
```html
<!-- Для игр -->
<div class="game-card" data-game="games/new_game.html">
    <span class="game-icon">🎲</span>
    <h2 class="game-title">Название игры</h2>
    <p class="game-description">Описание игры</p>
    <div class="game-controls">Управление</div>
</div>

<!-- Для программ -->
<div class="game-card" data-game="programs/new_program.html">
    <span class="game-icon">🔧</span>
    <h2 class="game-title">Название программы</h2>
    <p class="game-description">Описание программы</p>
    <div class="game-controls">Использование</div>
</div>
```

2. **Обновление счетчиков**
```html
<p>Всего игр: <span id="gameCount">5</span> | Программ: <span id="programCount">5</span></p>
```

## 🚀 Развертывание и использование

### Локальный запуск
1. Скачайте или клонируйте проект
2. Откройте `index.html` в любом современном браузере
3. Выберите игру или программу из лаунчера

### Веб-развертывание
1. Загрузите все файлы на любой веб-сервер
2. Убедитесь, что сервер корректно обслуживает статические файлы
3. Откройте `index.html` через веб-адрес

### Требования к браузеру
- **Минимальные**: Поддержка HTML5 Canvas, Web Audio API, localStorage
- **Рекомендуемые**: Chrome 70+, Firefox 65+, Safari 12+, Edge 79+
- **Мобильные**: iOS Safari 12+, Chrome Mobile 70+

## 📋 Чек-лист разработки

### ✅ Для каждой новой игры/программы:
- [ ] Русскоязычный интерфейс
- [ ] Автономный HTML файл
- [ ] Навигация назад к лаунчеру (кнопка ←)
- [ ] Адаптивный дизайн
- [ ] Поддержка touch-управления
- [ ] Звуковые эффекты
- [ ] Сохранение данных в localStorage
- [ ] Единообразный стиль дизайна
- [ ] Обработка всех типов ввода
- [ ] Предотвращение нежелательного поведения браузера
- [ ] Тестирование на мобильных устройствах

### ✅ Для интеграции в лаунчер:
- [ ] Добавлена карточка в `index.html`
- [ ] Правильный путь в `data-game` атрибуте
- [ ] Подходящая иконка и описание
- [ ] Информация об управлении
- [ ] Обновлены счетчики игр/программ

---

**Версия документации:** 2.0  
**Дата обновления:** 2024  
**Статус:** Активная разработка  
**Лицензия:** Открытый исходный код