<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
<title>Homestead Survival v3</title>
<style>
  body { margin:0; background:#1a1a1a; color:#fff; font-family:monospace; overflow:hidden; touch-action:none; }
  #game { position:relative; width:100vw; height:100vh; background:#2d4a2d; }
  #player { position:absolute; width:20px; height:20px; background:#ff4444; z-index:10; transition:all 0.1s; }
  .tree { position:absolute; width:30px; height:30px; background:#0a5; border-radius:50%; }
  .animal { position:absolute; width:18px; height:18px; border-radius:50%; }
  .sheep { background:#eee; }
  .deer { background:#a65; }
  #ui { position:absolute; top:10px; left:10px; font-size:18px; z-index:20; background:rgba(0,0,0,0.5); padding:5px; }
  #dpad { position:absolute; bottom:20px; right:20px; z-index:20; }
  #dpad button { width:60px; height:60px; margin:2px; font-size:24px; background:#333; color:#fff; border:1px solid #555; }
  #action { position:absolute; bottom:20px; left:20px; width:80px; height:80px; font-size:20px; z-index:20; background:#333; color:#fff; border:1px solid #555; }
</style>
</head>
<body>
<div id="game">
  <div id="player"></div>
  <div id="ui">🌳 0 🧵 0 🥫 0 💧 0 🪙 0</div>
  <div id="dpad">
    <button ontouchstart="move('up')" onclick="move('up')">↑</button><br>
    <button ontouchstart="move('left')" onclick="move('left')">←</button>
    <button ontouchstart="move('right')" onclick="move('right')">→</button><br>
    <button ontouchstart="move('down')" onclick="move('down')">↓</button>
  </div>
  <button id="action" ontouchstart="interact()" onclick="interact()">A</button>
</div>
<script>
let player = {x:200, y:200, wood:0, cloth:0, food:0, water:0, metal:0};
let trees = [], animals = [];
const game = document.getElementById('game');
const playerEl = document.getElementById('player');
const ui = document.getElementById('ui');

function updateUI(){
  ui.textContent = `🌳 ${player.wood} 🧵 ${player.cloth} 🥫 ${player.food} 💧 ${player.water} 🪙 ${player.metal}`;
}

function spawnTrees(){
  for(let i=0;i<8;i++){
    let t = document.createElement('div');
    t.className='tree';
    t.style.left=Math.random()*85+5+'%';
    t.style.top=Math.random()*85+5+'%';
    game.appendChild(t);
    trees.push(t);
  }
}

function spawnAnimals(){
  for(let i=0;i<3;i++){
    let s = document.createElement('div');
    s.className='animal sheep';
    s.style.left=Math.random()*85+5+'%';
    s.style.top=Math.random()*85+5+'%';
    s.dataset.type='sheep';
    game.appendChild(s);
    animals.push(s);
    
    let d = document.createElement('div');
    d.className='animal deer';
    d.style.left=Math.random()*85+5+'%';
    d.style.top=Math.random()*85+5+'%';
    d.dataset.type='deer';
    game.appendChild(d);
    animals.push(d);
  }
}

function move(dir){
  if(dir==='up'||dir==='w') player.y=Math.max(0,player.y-15);
  if(dir==='down'||dir==='s') player.y=Math.min(window.innerHeight-20,player.y+15);
  if(dir==='left'||dir==='a') player.x=Math.max(0,player.x-15);
  if(dir==='right'||dir==='d') player.x=Math.min(window.innerWidth-20,player.x+15);
  playerEl.style.left=player.x+'px';
  playerEl.style.top=player.y+'px';
}

function interact(){
  let px=player.x, py=player.y;
  trees.forEach((t,i)=>{
    let tx=parseInt(t.style.left)/100*window.innerWidth;
    let ty=parseInt(t.style.top)/100*window.innerHeight;
    if(Math.abs(px-tx)<40 && Math.abs(py-ty)<40){
      player.wood+=2;
      t.remove();
      trees.splice(i,1);
      updateUI();
    }
  });
  animals.forEach((a,i)=>{
    let ax=parseInt(a.style.left)/100*window.innerWidth;
    let ay=parseInt(a.style.top)/100*window.innerHeight;
    if(Math.abs(px-ax)<40 && Math.abs(py-ay)<40){
      if(a.dataset.type==='sheep'){ player.food+=1; player.cloth+=1; }
      if(a.dataset.type==='deer'){ player.food+=1; player.wood+=1; }
      a.remove();
      animals.splice(i,1);
      updateUI();
    }
  });
}

document.addEventListener('keydown',e=>{
  if(['w','a','s','d','ArrowUp','ArrowLeft','ArrowDown','ArrowRight'].includes(e.key)){
    e.preventDefault();
    let dir=e.key.replace('Arrow','').toLowerCase();
    move(dir);
  }
  if(e.key===' '||e.key==='Enter') interact();
});

playerEl.style.left=player.x+'px';
playerEl.style.top=player.y+'px';
spawnTrees();
spawnAnimals();
updateUI();
setInterval(()=>{ if(trees.length<8) spawnTrees(); },120000);
</script>
</body>
</html>
