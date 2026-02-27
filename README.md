<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Tokyo 3D Boss Cat Billboard</title>
<style>
    body {
        margin: 0;
        overflow: hidden;
        background: #000;
    }
    canvas {
        display: block;
    }
</style>
</head>
<body>
<script src="https://cdn.jsdelivr.net/npm/three@0.160.0/build/three.min.js"></script>

<script>
const scene = new THREE.Scene();
scene.background = new THREE.Color(0x000000);

const camera = new THREE.PerspectiveCamera(50, window.innerWidth/window.innerHeight, 0.1, 1000);
camera.position.set(0, 1.5, 6);

const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setPixelRatio(window.devicePixelRatio);
document.body.appendChild(renderer.domElement);

// Lighting
const ambient = new THREE.AmbientLight(0xffffff, 0.6);
scene.add(ambient);

const directional = new THREE.DirectionalLight(0xffffff, 1);
directional.position.set(5, 10, 7);
scene.add(directional);

const rimLight = new THREE.PointLight(0xffd700, 2, 10);
rimLight.position.set(0, 2, 3);
scene.add(rimLight);

// Curved LED Screen
const screenGeometry = new THREE.CylinderGeometry(8, 8, 5, 64, 1, true, Math.PI * 1.25, Math.PI/2);
const screenMaterial = new THREE.MeshStandardMaterial({
    color: 0x111111,
    roughness: 0.8,
    metalness: 0.2,
});
const screen = new THREE.Mesh(screenGeometry, screenMaterial);
screen.position.y = 1.5;
scene.add(screen);

// LED subtle shimmer
const ledOverlay = new THREE.Mesh(
    screenGeometry.clone(),
    new THREE.MeshBasicMaterial({
        color: 0x222222,
        wireframe: true,
        transparent: true,
        opacity: 0.05
    })
);
ledOverlay.position.copy(screen.position);
scene.add(ledOverlay);

// Cat Group
const cat = new THREE.Group();
scene.add(cat);

// Body
const body = new THREE.Mesh(
    new THREE.BoxGeometry(2.2, 1.2, 1),
    new THREE.MeshStandardMaterial({ color: 0x444444, roughness: 0.5 })
);
body.position.set(0, 1.5, 0.6);
cat.add(body);

// Head
const head = new THREE.Mesh(
    new THREE.SphereGeometry(0.6, 32, 32),
    new THREE.MeshStandardMaterial({ color: 0x555555 })
);
head.position.set(1.3, 2.2, 0.8);
cat.add(head);

// Gold Chain
const chain = new THREE.Mesh(
    new THREE.TorusGeometry(0.6, 0.08, 16, 100),
    new THREE.MeshStandardMaterial({
        color: 0xffd700,
        metalness: 1,
        roughness: 0.2,
        emissive: 0xaa8800,
        emissiveIntensity: 0.4
    })
);
chain.rotation.x = Math.PI / 2;
chain.position.set(1.3, 1.9, 1.1);
cat.add(chain);

// Legs
const legGeometry = new THREE.BoxGeometry(0.3, 1, 0.3);
const legMaterial = new THREE.MeshStandardMaterial({ color: 0x333333 });

const leg1 = new THREE.Mesh(legGeometry, legMaterial);
const leg2 = leg1.clone();
leg1.position.set(-0.5, 0.8, 0.8);
leg2.position.set(0.5, 0.8, 0.8);
cat.add(leg1, leg2);

// Ground Glow
const glow = new THREE.Mesh(
    new THREE.CircleGeometry(1.5, 32),
    new THREE.MeshBasicMaterial({
        color: 0xffd700,
        transparent: true,
        opacity: 0.4
    })
);
glow.rotation.x = -Math.PI / 2;
glow.position.set(0, 0.76, 1.2);
cat.add(glow);

// Animation
let clock = new THREE.Clock();

function animate() {
    requestAnimationFrame(animate);
    const t = clock.getElapsedTime();

    // Boss walk movement
    cat.position.x = Math.sin(t * 0.5) * 2;
    cat.position.z = Math.abs(Math.sin(t * 0.5)) * 1.2;

    leg1.rotation.x = Math.sin(t * 4) * 0.5;
    leg2.rotation.x = -Math.sin(t * 4) * 0.5;

    chain.rotation.z = Math.sin(t * 3) * 0.2;

    // Subtle billboard camera parallax illusion
    camera.position.x = Math.sin(t * 0.3) * 0.5;
    camera.lookAt(0, 1.5, 0);

    renderer.render(scene, camera);
}

animate();

// Resize Handling
window.addEventListener("resize", () => {
    camera.aspect = window.innerWidth/window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
});
</script>
</body>
</html>
