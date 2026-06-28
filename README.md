<!DOCTYPE html>
<html lang="fa">
<head>
<meta charset="UTF-8">
<title>Big Mario Map + Huge Buttons</title>

<style>
    body { margin:0; background:#87ceeb; overflow:hidden; }

    /* قاب دوربین */
    #viewport {
        position:absolute;
        top:0; left:0;
        width:100vw;
        height:100vh;
        overflow:hidden;
        background:#87ceeb;
    }

    /* مپ خیلی بزرگ */
    #game {
        position:relative;
        width:3000px;
        height:1000px;
        background:#5ec14a;
    }

    .platform {
        position:absolute;
        height:20px;
        background:#8b5a2b;
    }

    #player {
        position:absolute;
        width:30px;
        height:40px;
        background:red;
        border:3px solid #000;
        border-radius:5px;
    }

    /* دکمه‌های خیلی بزرگ (ثابت روی صفحه) */
    #controls {
        position:fixed;
        bottom:30px;
        left:50%;
        transform:translateX(-50%);
        display:flex;
        gap:40px;
        z-index:999999;
    }

    .btn {
        width:110px;
        height:110px;
        background:#ffffffcc;
        border:4px solid #000;
        border-radius:50%;
        display:flex;
        align-items:center;
        justify-content:center;
        font-size:55px;
        font-weight:bold;
        user-select:none;
        touch-action:none;
    }
</style>
</head>

<body>

<div id="viewport">
    <div id="game">

        <!-- بازیکن -->
        <div id="player"></div>

        <!-- زمین اصلی -->
        <div class="platform" style="left:0; bottom:0; width:3000px;"></div>

        <!-- پلتفرم‌ها -->
        <div class="platform" style="left:200px; bottom:150px; width:200px;"></div>
        <div class="platform" style="left:600px; bottom:250px; width:180px;"></div>
        <div class="platform" style="left:900px; bottom:180px; width:220px;"></div>
        <div class="platform" style="left:1200px; bottom:300px; width:240px;"></div>
        <div class="platform" style="left:1600px; bottom:200px; width:200px;"></div>
        <div class="platform" style="left:2000px; bottom:260px; width:180px;"></div>
        <div class="platform" style="left:2400px; bottom:150px; width:300px;"></div>

    </div>
</div>

<!-- دکمه‌های بزرگ روی صفحه -->
<div id="controls">
    <div class="btn" id="left">←</div>
    <div class="btn" id="right">→</div>
    <div class="btn" id="up">↑</div>
</div>

<script>
const player = document.getElementById("player");
const viewport = document.getElementById("viewport");

let x = 50, y = 0, vx = 0, vy = 0;
let gravity = 0.5;
let onGround = false;
let keys = {};

// دکمه‌های موبایل
function bindButton(id, key){
    let b = document.getElementById(id);
    b.addEventListener("touchstart", ()=> keys[key] = true);
    b.addEventListener("touchend", ()=> keys[key] = false);
}

bindButton("left","ArrowLeft");
bindButton("right","ArrowRight");
bindButton("up","Jump");

// دکمه های کیبورد
document.addEventListener("keydown", e=> keys[e.key] = true);
document.addEventListener("keyup", e=> keys[e.key] = false);

function gameLoop(){
    
    // حرکت چپ راست
    if(keys["ArrowLeft"]) vx = -3;
    else if(keys["ArrowRight"]) vx = 3;
    else vx = 0;

    // پرش
    if(keys["Jump"] && onGround){
        vy = -18;  // پرش بلند
        onGround = false;
    }

    // گرانش و حرکت
    vy += gravity;
    x += vx;
    y += vy;

    // محدودیت
    if(x < 0) x = 0;
    if(x > 2970) x = 2970;

    // برخورد با پلتفرم‌ها
    onGround = false;

    document.querySelectorAll(".platform").forEach(p => {

        let rect = p.getBoundingClientRect();
        let px = x - viewport.scrollLeft;
        let py = y + 40 - viewport.scrollTop;

        if(px + 30 > rect.left &&
           px < rect.right &&
           py > rect.top &&
           py < rect.top + 20 &&
           vy > 0){

            y = rect.top + viewport.scrollTop - 40;
            vy = 0;
            onGround = true;
        }
    });

    // اعمال روی پلیر
    player.style.left = x + "px";
    player.style.top  = y + "px";

    // اسکرول دوربین
    viewport.scrollLeft = x - window.innerWidth/2;
    viewport.scrollTop  = y - window.innerHeight/2;

    requestAnimationFrame(gameLoop);
}

gameLoop();
</script>

</body>
</html>

