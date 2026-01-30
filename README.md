# Dost-
you are best gaming<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>3D Car Evolution</title>
    <style>
        body { margin: 0; overflow: hidden; background: #87CEEB; font-family: 'Segoe UI', sans-serif; }
        #ui { position: absolute; top: 20px; left: 20px; color: black; background: rgba(255,255,255,0.7); padding: 15px; border-radius: 10px; }
        #msg { color: #d35400; font-weight: bold; }
    </style>
</head>
<body>
    <div id="ui">
        <div>Level: <span id="level">Mini Car</span></div>
        <div>Size: <span id="score">1</span></div>
        <div id="msg">Collect Yellow Cubes to GROW!</div>
    </div>

    <script type="module">
        import * as THREE from 'https://cdn.skypack.dev/three@0.136.0';

        // --- SCENE SETUP ---
        const scene = new THREE.Scene();
        scene.background = new THREE.Color(0x87CEEB); // Blue sky
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        const renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        document.body.appendChild(renderer.domElement);

        // --- PLAYER (CAR) ---
        let carSize = 1;
        const geometry = new THREE.BoxGeometry(1, 0.6, 1.5);
        const material = new THREE.MeshStandardMaterial({ color: 0xff0000 });
        const car = new THREE.Mesh(geometry, material);
        car.position.y = 0.3;
        scene.add(car);

        // --- GROUND ---
        const ground = new THREE.Mesh(
            new THREE.PlaneGeometry(20, 2000),
            new THREE.MeshStandardMaterial({ color: 0x333333 })
        );
        ground.rotation.x = -Math.PI / 2;
        scene.add(ground);

        // --- LIGHTS ---
        const light = new THREE.DirectionalLight(0xffffff, 1);
        light.position.set(5, 10, 5);
        scene.add(light);
        scene.add(new THREE.AmbientLight(0x404040));

        camera.position.set(0, 5, 10);
        camera.lookAt(car.position);

        // --- SPAWN ITEMS (OBSTACLES/FOOD) ---
        const items = [];
        function createItem() {
            const itemGeo = new THREE.BoxGeometry(0.5, 0.5, 0.5);
            const itemMat = new THREE.MeshStandardMaterial({ color: 0xffff00 });
            const item = new THREE.Mesh(itemGeo, itemMat);
            item.position.set((Math.random() - 0.5) * 10, 0.25, car.position.z - 50);
            scene.add(item);
            items.push(item);
        }
        setInterval(createItem, 1000); // Har second ek item

        // --- CONTROLS ---
        const keys = { Left: false, Right: false };
        window.onkeydown = (e) => { if(e.key.includes("Arrow")) keys[e.key.replace("Arrow", "")] = true; };
        window.onkeyup = (e) => { if(e.key.includes("Arrow")) keys[e.key.replace("Arrow", "")] = false; };

        // --- MAIN LOOP ---
        function animate() {
            requestAnimationFrame(animate);

            // Car Forward Movement
            car.position.z -= 0.2;
            camera.position.z = car.position.z + 10;
            camera.lookAt(car.position);

            // Left/Right
            if(keys.Left && car.position.x > -4) car.position.x -= 0.15;
            if(keys.Right && car.position.x < 4) car.position.x += 0.15;

            // Collision & Evolution Logic
            items.forEach((item, index) => {
                if(car.position.distanceTo(item.position) < carSize) {
                    scene.remove(item);
                    items.splice(index, 1);
                    
                    // Grow Car!
                    carSize += 0.1;
                    car.scale.set(carSize, carSize, carSize);
                    car.position.y = 0.3 * carSize;
                    
                    document.getElementById('score').innerText = carSize.toFixed(1);

                    // Evolution Labels
                    if(carSize > 3) document.getElementById('level').innerText = "MONSTER TRUCK!";
                    else if(carSize > 2) document.getElementById('level').innerText = "BIG SUV";
                    else if(carSize > 1.5) document.getElementById('level').innerText = "SEDAN";
                }
            });

            renderer.render(scene, camera);
        }
        animate();
    </script>
</body>
</html>

