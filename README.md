<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="UTF-8">
<title>RPG 3D Shooter</title>
<style>
  body { margin: 0; overflow: hidden; background: #222; }
  #mobile-controls { position: fixed; bottom: 10px; width: 100%; text-align: center; z-index: 10; }
  .button { width: 60px; height: 60px; margin: 5px; background: #555; color: white; border-radius: 10px; font-size: 20px; display: inline-block; line-height: 60px; user-select: none; }
</style>
</head>
<body>

<div id="mobile-controls">
  <div class="button" id="up">↑</div>
  <div class="button" id="left">←</div>
  <div class="button" id="down">↓</div>
  <div class="button" id="right">→</div>
  <div class="button" id="shoot">💥</div>
</div>

<script src="https://cdn.jsdelivr.net/npm/three@0.153.0/build/three.min.js"></script>
<script>
let scene = new THREE.Scene();
let camera = new THREE.PerspectiveCamera(75, window.innerWidth/window.innerHeight, 0.1, 1000);
let renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

// Player
let playerGeometry = new THREE.BoxGeometry(1,1,1);
let playerMaterial = new THREE.MeshBasicMaterial({color: 0x0000ff});
let player = new THREE.Mesh(playerGeometry, playerMaterial);
scene.add(player);
player.position.y = 0.5;

// Bullets
let bullets = [];

// Ground
let groundGeometry = new THREE.PlaneGeometry(50,50);
let groundMaterial = new THREE.MeshBasicMaterial({color: 0x555555, side: THREE.DoubleSide});
let ground = new THREE.Mesh(groundGeometry, groundMaterial);
ground.rotation.x = Math.PI/2;
scene.add(ground);

// Camera
camera.position.set(0,5,10);
camera.lookAt(player.position);

// Movement
let keys = {};
document.addEventListener('keydown', (e) => keys[e.key.toLowerCase()] = true);
document.addEventListener('keyup', (e) => keys[e.key.toLowerCase()] = false);

// Mobile controls
['up','down','left','right'].forEach(dir=>{
  let el = document.getElementById(dir);
  el.addEventListener('touchstart',()=>keys['arrow'+dir]=true);
  el.addEventListener('touchend',()=>keys['arrow'+dir]=false);
});
document.getElementById('shoot').addEventListener('touchstart', shoot);
document.addEventListener('keydown', e=>{if(e.key.toLowerCase()==='f') shoot();});

// Shoot function
function shoot(){
  let bulletGeometry = new THREE.BoxGeometry(0.2,0.2,0.2);
  let bulletMaterial = new THREE.MeshBasicMaterial({color:0xffff00});
  let bullet = new THREE.Mesh(bulletGeometry, bulletMaterial);
  bullet.position.set(player.position.x, player.position.y, player.position.z);
  bullets.push({mesh: bullet, speed:0.5});
  scene.add(bullet);
}

// Animate
function animate(){
  requestAnimationFrame(animate);

  // Movement
  if(keys['w'] || keys['arrowup']) player.position.z -= 0.1;
  if(keys['s'] || keys['arrowdown']) player.position.z += 0.1;
  if(keys['a'] || keys['arrowleft']) player.position.x -= 0.1;
  if(keys['d'] || keys['arrowright']) player.position.x += 0.1;

  // Update bullets
  bullets.forEach((b,i)=>{
    b.mesh.position.z -= b.speed;
    if(b.mesh.position.z < -25) {
      scene.remove(b.mesh);
      bullets.splice(i,1);
    }
  });

  camera.position.set(player.position.x, 5, player.position.z + 10);
  camera.lookAt(player.position);

  renderer.render(scene, camera);
}

animate();
</script>
</body>
</html>
