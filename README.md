<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<title>Reto Digital - Comunicación</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<style>
body{
    margin:0;
    background:#111;
    overflow:hidden;
    font-family:Arial, sans-serif;
}

#game{
    width:100vw;
    height:100vh;
    position:relative;
}

.road{
    width:60%;
    height:100%;
    background:#333;
    margin:auto;
    position:relative;
    overflow:hidden;
    border-left:5px solid white;
    border-right:5px solid white;
}

.line{
    position:absolute;
    width:10px;
    height:80px;
    background:white;
    left:50%;
    transform:translateX(-50%);
}

#car{
    width:50px;
    height:90px;
    background:red;
    position:absolute;
    bottom:20px;
    left:50%;
    transform:translateX(-50%);
    border-radius:10px;
}

.enemy{
    width:50px;
    height:90px;
    background:yellow;
    position:absolute;
    top:-100px;
    border-radius:10px;
}

#score{
    position:fixed;
    top:10px;
    left:10px;
    color:white;
    font-size:22px;
    z-index:10;
}

.overlay{
    position:fixed;
    inset:0;
    background:rgba(0,0,0,0.85);
    display:flex;
    align-items:center;
    justify-content:center;
    flex-direction:column;
    color:white;
    z-index:20;
}

button{
    padding:10px 20px;
    font-size:18px;
    border:none;
    background:red;
    color:white;
    border-radius:5px;
    cursor:pointer;
    margin-top:15px;
}
</style>
</head>
<body>

<div id="score">Puntos: 0</div>

<div id="startScreen" class="overlay">
    <h1>🚗 Reto del Comunicador Digital</h1>
    <p>Esquiva los obstáculos y demuestra tus reflejos digitales</p>
    <button onclick="startGame()">Comenzar</button>
</div>

<div id="game">
    <div class="road" id="road">
        <div id="car"></div>
    </div>
</div>

<div id="gameOver" class="overlay" style="display:none;">
    <h1>💥 Juego Terminado</h1>
    <div id="finalScore"></div>
    <button onclick="restartGame()">Reiniciar</button>
</div>

<script>
const car = document.getElementById("car");
const road = document.getElementById("road");
const scoreText = document.getElementById("score");
const gameOver = document.getElementById("gameOver");
const finalScore = document.getElementById("finalScore");
const startScreen = document.getElementById("startScreen");

let carX;
let score;
let speed;
let playing = false;

function startGame(){
    startScreen.style.display = "none";
    init();
}

function init(){
    carX = road.offsetWidth/2 - 25;
    car.style.left = carX + "px";
    score = 0;
    speed = 5;
    playing = true;
    scoreText.textContent = "Puntos: 0";
}

function restartGame(){
    gameOver.style.display="none";
    document.querySelectorAll(".enemy").forEach(e=>e.remove());
    init();
}

document.addEventListener("keydown", e=>{
    if(!playing) return;
    if(e.key==="ArrowLeft") carX -= 25;
    if(e.key==="ArrowRight") carX += 25;
    limitCar();
});

road.addEventListener("touchmove", e=>{
    if(!playing) return;
    const touchX = e.touches[0].clientX;
    carX = touchX - road.offsetLeft - 25;
    limitCar();
});

function limitCar(){
    carX = Math.max(0, Math.min(carX, road.offsetWidth-50));
    car.style.left = carX+"px";
}

function spawnEnemy(){
    if(!playing) return;

    const enemy = document.createElement("div");
    enemy.className="enemy";
    enemy.style.left = Math.random()*(road.offsetWidth-50)+"px";
    road.appendChild(enemy);

    let y=-100;

    const move=setInterval(()=>{
        if(!playing){
            clearInterval(move);
            return;
        }

        y+=speed;
        enemy.style.top=y+"px";

        const eRect=enemy.getBoundingClientRect();
        const cRect=car.getBoundingClientRect();

        if(
            eRect.bottom>cRect.top &&
            eRect.top<cRect.bottom &&
            eRect.left<cRect.right &&
            eRect.right>cRect.left
        ){
            playing=false;
            finalScore.textContent="Puntuación final: "+score;
            gameOver.style.display="flex";
            clearInterval(move);
        }

        if(y>window.innerHeight){
            enemy.remove();
            score++;
            scoreText.textContent="Puntos: "+score;
            speed+=0.2;
            clearInterval(move);
        }
    },20);
}

setInterval(spawnEnemy,900);
</script>

</body>
</html>
