# Blocxy-snake-<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Blocky Snake</title>

<style>
    :root {
        --bg: #78a64b;
    }

    * {
        box-sizing: border-box;
    }

    body {
        margin: 0;
        min-height: 100vh;
        background: #111;
        color: white;
        font-family: Arial, sans-serif;
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .screen {
        display: none;
        width: min(900px, 95vw);
    }

    .screen.active {
        display: block;
    }

    .menu {
        background: #242424;
        border: 4px solid #111;
        box-shadow: 8px 8px #000;
        padding: 35px;
        text-align: center;
        max-width: 550px;
        margin: auto;
    }

    h1 {
        font-size: 60px;
        margin: 0 0 30px;
        text-shadow: 5px 5px #000;
    }

    button {
        width: 100%;
        padding: 16px;
        margin: 10px 0;
        border: 3px solid #222;
        background: #666;
        color: white;
        font-size: 20px;
        font-weight: bold;
        cursor: pointer;
        box-shadow: 4px 4px #000;
    }

    button:hover {
        background: #777;
    }

    button:active {
        transform: translate(3px, 3px);
        box-shadow: 1px 1px #000;
    }

    .settings {
        text-align: left;
    }

    .setting {
        margin: 25px 0;
    }

    .setting label {
        display: flex;
        justify-content: space-between;
        margin-bottom: 8px;
    }

    input[type="range"] {
        width: 100%;
    }

    .gamebox {
        background: #222;
        padding: 12px;
        border: 4px solid #111;
        box-shadow: 7px 7px #000;
    }

    .hud {
        display: flex;
        justify-content: space-between;
        gap: 8px;
        flex-wrap: wrap;
        margin-bottom: 10px;
        font-weight: bold;
    }

    .hud span {
        background: #333;
        padding: 8px 12px;
        border: 2px solid #111;
    }

    canvas {
        display: block;
        width: 100%;
        height: auto;
        background: var(--bg);
        border: 4px solid #111;
        image-rendering: pixelated;
    }

    .help {
        text-align: center;
        margin-top: 10px;
        opacity: .8;
    }

    .modal {
        position: fixed;
        inset: 0;
        background: rgba(0,0,0,.75);
        display: none;
        justify-content: center;
        align-items: center;
        padding: 20px;
    }

    .modal.show {
        display: flex;
    }

    .modalbox {
        background: #242424;
        border: 4px solid #111;
        box-shadow: 8px 8px #000;
        padding: 30px;
        text-align: center;
        width: min(420px, 90vw);
    }

    .xpbar {
        width: 150px;
        height: 18px;
        display: inline-block;
        background: #111;
        border: 2px solid #000;
        vertical-align: middle;
    }

    .xpfill {
        display: block;
        height: 100%;
        width: 0%;
        background: #62d94b;
    }
</style>
</head>

<body>

<!-- ГЛАВНОЕ МЕНЮ -->
<section id="menu" class="screen active">
    <div class="menu">
        <h1>BLOCKY SNAKE</h1>

        <button id="play">ИГРАТЬ</button>
        <button id="settingsBtn">НАСТРОЙКИ</button>

        <p>Управление: WASD или стрелки</p>
    </div>
</section>


<!-- НАСТРОЙКИ -->
<section id="settings" class="screen">
    <div class="menu settings">

        <h2>НАСТРОЙКИ</h2>

        <div class="setting">
            <label>
                🔊 Звук
                <span id="soundValue">80%</span>
            </label>

            <input
                id="sound"
                type="range"
                min="0"
                max="100"
                value="80"
            >
        </div>

        <div class="setting">
            <label>
                ☀️ Яркость
                <span id="brightnessValue">100%</span>
            </label>

            <input
                id="brightness"
                type="range"
                min="50"
                max="130"
                value="100"
            >
        </div>

        <div class="setting">
            <label>
                🎨 Цвет поля
            </label>

            <input
                id="bgColor"
                type="color"
                value="#78a64b"
            >
        </div>

        <button id="back">НАЗАД</button>
    </div>
</section>


<!-- ИГРА -->
<section id="game" class="screen">

    <div class="gamebox">

        <div class="hud">

            <span>
                ❤️ <span id="health">10</span>
            </span>

            <span>
                🍗 Голод:
                <span id="hunger">10</span>
            </span>

            <span>
                Ур. <span id="level">1</span>
            </span>

            <span>
                XP:
                <span id="xp">0</span>
            </span>

            <span>
                ⏱ <span id="time">0</span> сек
            </span>

        </div>

        <canvas
            id="canvas"
            width="800"
            height="512"
        ></canvas>

        <div class="help">
            Собирай яблоки 🍎, чтобы восполнять голод.
            XP также получается за время нахождения на карте.
        </div>

    </div>

</section>


<!-- ОКНО СМЕРТИ -->
<div id="gameOver" class="modal">

    <div class="modalbox">

        <h2>ИГРА ОКОНЧЕНА</h2>

        <p>
            Голод закончился!
        </p>

        <p>
            Уровень:
            <b id="finalLevel">1</b>
        </p>

        <p>
            XP:
            <b id="finalXp">0</b>
        </p>

        <button id="again">
            ИГРАТЬ СНОВА
        </button>

        <button id="toMenu">
            В МЕНЮ
        </button>

    </div>

</div>


<script>

const canvas = document.getElementById("canvas");
const ctx = canvas.getContext("2d");

const menu = document.getElementById("menu");
const settings = document.getElementById("settings");
const game = document.getElementById("game");

const gameOver = document.getElementById("gameOver");

const playButton = document.getElementById("play");
const settingsButton = document.getElementById("settingsBtn");
const backButton = document.getElementById("back");

const againButton = document.getElementById("again");
const menuButton = document.getElementById("toMenu");

const healthText = document.getElementById("health");
const hungerText = document.getElementById("hunger");
const levelText = document.getElementById("level");
const xpText = document.getElementById("xp");
const timeText = document.getElementById("time");

const finalLevel = document.getElementById("finalLevel");
const finalXp = document.getElementById("finalXp");


// Размер игровой сетки
const GRID_WIDTH = 25;
const GRID_HEIGHT = 16;


// Настройки
let sound = 80;
let brightness = 100;
let backgroundColor = "#78a64b";


// Игровые переменные
let snake;
let apple;

let direction;
let nextDirection;

let hunger = 10;

let xp = 0;
let level = 1;

let seconds = 0;

let gameRunning = false;

let lastMove = 0;
let lastSecond = 0;


// Переключение экранов
function showScreen(screen) {

    menu.classList.remove("active");
    settings.classList.remove("active");
    game.classList.remove("active");

    screen.classList.add("active");
}


// Случайное число
function randomNumber(max) {
    return Math.floor(Math.random() * max);
}


// Создание яблока
function spawnApple() {

    do {

        apple = {
            x: randomNumber(GRID_WIDTH),
            y: randomNumber(GRID_HEIGHT)
        };

    } while (
        snake.some(
            part =>
                part.x === apple.x &&
                part.y === apple.y
        )
    );
}


// Начало игры
function startGame() {

    snake = [

        { x: 12, y: 8 },
        { x: 11, y: 8 },
        { x: 10, y: 8 }

    ];

    direction = {
        x: 1,
        y: 0
    };

    nextDirection = {
        x: 1,
        y: 0
    };

    hunger = 10;

    xp = 0;

    level = 1;

    seconds = 0;

    spawnApple();

    gameRunning = true;

    gameOver.classList.remove("show");

    showScreen(game);

    updateHUD();

    lastMove = performance.now();
    lastSecond = performance.now();

    requestAnimationFrame(gameLoop);
}


// Обновление интерфейса
function updateHUD() {

    healthText.textContent = hunger;

    hungerText.textContent = hunger;

    levelText.textContent = level;

    xpText.textContent = xp;

    timeText.textContent = seconds;
}


// Движение змейки
function moveSnake() {

    direction = nextDirection;

    const head = {
        x: snake[0].x + direction.x,
        y: snake[0].y + direction.y
    };


    // Столкновение со стеной
    // Вместо выхода за границу змейка просто не движется дальше.
    if (
        head.x < 0 ||
        head.x >= GRID_WIDTH ||
        head.y < 0 ||
        head.y >= GRID_HEIGHT
    ) {

        return;
    }


    // Столкновение с собой
    if (
        snake.some(
            part =>
                part.x === head.x &&
                part.y === head.y
        )
    ) {

        return;
    }


    snake.unshift(head);


    // Яблоко собрано
    if (
        head.x === apple.x &&
        head.y === apple.y
    ) {

        // Восстанавливаем голод
        hunger = Math.min(10, hunger + 2);

        // Дополнительный XP
        xp += 25;

        checkLevel();

        spawnApple();

    } else {

        snake.pop();

    }
}


// Проверка уровня
function checkLevel() {

    const neededXP = level * 100;

    if (xp >= neededXP) {

        level++;

    }
}


// Рисование
function draw() {

    const cellWidth =
        canvas.width / GRID_WIDTH;

    const cellHeight =
        canvas.height / GRID_HEIGHT;


    // Фон
    ctx.fillStyle = backgroundColor;

    ctx.fillRect(
        0,
        0,
        canvas.width,
        canvas.height
    );


    // Minecraft-подобная клетчатая земля
    for (
        let y = 0;
        y < GRID_HEIGHT;
        y++
    ) {

        for (
            let x = 0;
            x < GRID_WIDTH;
            x++
        ) {

            if ((x + y) % 2 === 0) {

                ctx.fillStyle =
                    "rgba(255,255,255,0.05)";

                ctx.fillRect(
                    x * cellWidth,
                    y * cellHeight,
                    cellWidth,
                    cellHeight
                );

            }

        }

    }


    // Сетка
    ctx.strokeStyle =
        "rgba(0,0,0,0.12)";

    for (
        let x = 0;
        x <= GRID_WIDTH;
        x++
    ) {

        ctx.beginPath();

        ctx.moveTo(
            x * cellWidth,
            0
        );

        ctx.lineTo(
            x * cellWidth,
            canvas.height
        );

        ctx.stroke();

    }


    for (
        let y = 0;
        y <= GRID_HEIGHT;
        y++
    ) {

        ctx.beginPath();

        ctx.moveTo(
            0,
            y * cellHeight
        );

        ctx.lineTo(
            canvas.width,
            y * cellHeight
        );

        ctx.stroke();

    }


    // Яблоко
    ctx.fillStyle = "#d93636";

    ctx.fillRect(
        apple.x * cellWidth + 9,
        apple.y * cellHeight + 9,
        cellWidth - 18,
        cellHeight - 18
    );


    // Стебель яблока
    ctx.fillStyle = "#704020";

    ctx.fillRect(
        apple.x * cellWidth +
        cellWidth / 2,
        apple.y * cellHeight + 3,
        7,
        10
    );


    // Змейка
    snake.forEach((part, index) => {

        if (index === 0) {

            ctx.fillStyle = "#2d8f32";

        } else {

            ctx.fillStyle = "#49b34d";

        }

        ctx.fillRect(
            part.x * cellWidth + 3,
            part.y * cellHeight + 3,
            cellWidth - 6,
            cellHeight - 6
        );


        // Пиксельная деталь
        ctx.fillStyle = "#226b27";

        ctx.fillRect(
            part.x * cellWidth + 6,
            part.y * cellHeight + 6,
            7,
            7
        );

    });

}


// Игровой цикл
function gameLoop(time) {

    if (!gameRunning) {
        return;
    }


    // Чем выше уровень, тем быстрее змейка
    const moveDelay =
        Math.max(
            65,
            150 - (level - 1) * 7
        );


    if (
        time - lastMove >= moveDelay
    ) {

        moveSnake();

        lastMove = time;

    }


    // Каждую секунду
    if (
        time - lastSecond >= 1000
    ) {

        seconds++;


        /*
         * XP за время нахождения на карте.
         * 1 секунда = 1 XP.
         */
        xp++;

        checkLevel();


        /*
         * 10 полосок голода исчезают
         * за 15 секунд.
         */
        hunger = Math.max(
            0,
            10 - Math.floor(seconds / 1.5)
        );


        updateHUD();

        lastSecond = time;


        // Голод закончился
        if (hunger <= 0) {

            endGame();

            return;

        }

    }


    draw();

    requestAnimationFrame(gameLoop);
}


// Конец игры
function endGame() {

    gameRunning = false;

    finalLevel.textContent = level;

    finalXp.textContent = xp;

    gameOver.classList.add("show");
}


// Управление
document.addEventListener(
    "keydown",
    event => {

        const key =
            event.key.toLowerCase();


        const controls = {

            "arrowup": [0, -1],
            "w": [0, -1],

            "arrowdown": [0, 1],
            "s": [0, 1],

            "arrowleft": [-1, 0],
            "a": [-1, 0],

            "arrowright": [1, 0],
            "d": [1, 0]

        };


        if (!controls[key]) {
            return;
        }


        event.preventDefault();


        const [x, y] =
            controls[key];


        // Нельзя развернуться
        // прямо в собственное тело.
        if (
            x !== -direction.x ||
            y !== -direction.y
        ) {

            nextDirection = {
                x,
                y
            };

        }

    }
);


// Кнопка "Играть"
playButton.onclick = startGame;


// Кнопка "Играть снова"
againButton.onclick = startGame;


// Кнопка настроек
settingsButton.onclick = () => {

    showScreen(settings);

};


// Назад
backButton.onclick = () => {

    showScreen(menu);

};


// В меню после смерти
menuButton.onclick = () => {

    gameRunning = false;

    gameOver.classList.remove("show");

    showScreen(menu);

};


// Звук
const soundInput =
    document.getElementById("sound");

const soundValue =
    document.getElementById("soundValue");

soundInput.oninput = event => {

    sound = Number(event.target.value);

    soundValue.textContent =
        sound + "%";

};


// Яркость
const brightnessInput =
    document.getElementById("brightness");

const brightnessValue =
    document.getElementById("brightnessValue");

brightnessInput.oninput = event => {

    brightness =
        Number(event.target.value);

    brightnessValue.textContent =
        brightness + "%";

    canvas.style.filter =
        `brightness(${brightness}%)`;

};


// Цвет фона
const bgColor =
    document.getElementById("bgColor");

bgColor.oninput = event => {

    backgroundColor =
        event.target.value;

};


// Первый кадр
draw();

</script>

</body>
</html>
