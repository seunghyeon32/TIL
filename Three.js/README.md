# Three.js

[TOC]



## Renderer 

**필수적으로 넣어줘야 함 !!**

```jsx
const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
// antialias: true => 부드럽게
// alpha: true => 배경을 투명으로

// 화면 사이즈를 정해준다
renderer.setSize(window.innerWidth, window.innerHeight);

//body 태그에 넣는다
document.body.appendChild(renderer.domElement);
```

- 화면을 찍어내는 역할



## Camera

✔ [three.js camera docs](https://threejs.org/manual/#en/cameras)

### 🤍 `THREE.PerspectiveCamera` | 원근 카메라

```jsx
// PerspectiveCamera( fov : Number, aspect : Number, near : Number, far : Number )
const camera = new THREE.PerspectiveCamera(
    25,                  
    window.innerWidth / window.innerHeight,
    0.1,
    1000
);
```

- `fov` 광각

  - 숫자가 증가할 수록 더 멀리까지 바라봄
  - 보통 작업 시, 70정도로 두고 작업함

- `aspect` 종횡비

  - 가로와 세로의 비
  - 화면의 비율

- `near/far`

  - near | 가까이 어디서부터 보이는가
  - far | 어디까지 멀리보이는가

  ![0.1 ~ 50까지 보인다](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8278d888-fa36-4bf9-8624-e771602dc221/Untitled.png)

  0.1 ~ 50까지 보인다

### 🤍 `camera.position` | 카메라 위치

```jsx
camera.position.z = 5;
```

- fov로 원근감을 지정해두고 position을 통해 카메라 위치를 변경



## Helper

### 🤍 Grid Helper

```jsx
const gridHelper = new THREE.GridHelper(20, 10);
// 몇 칸으로 나눌 것인가
// (그리드의 크기, 칸 수)

scene.add(gridHelper);
```

### 🤍 Camera Helper

```jsx
const camera = new THREE.PerspectiveCamera( 
	75, 
	window.innerWidth / window.innerHeight, 
	0.1, 
	1000 
);

const helper = new THREE.CameraHelper( camera );
scene.add( helper );
```

### 🤍 SpotLight Helper

```jsx
const spotLight = new THREE.SpotLight( 0xffffff );
spotLight.position.set( 10, 10, 10 );
scene.add( spotLight );

const spotLightHelper = new THREE.SpotLightHelper( spotLight );
scene.add( spotLightHelper );
```



## Material

- 재질에 따라 빛의 영향을 받는 정도에 차이가 생김

### 🤍 `MeshPhongMaterial`

- 가장 많이 사용하는 material
- 그림자, 반사광이 존재

✔ [three.js MeshPhongMaterial docs](https://threejs.org/docs/#api/en/materials/MeshPhongMaterial)



## Controls

### 🤍 **`OrbitControls` | 마우스로 카메라 제어**

✔ [three.js OrbitControls docs](https://threejs.org/docs/?q=Orbit#examples/ko/controls/OrbitControls)

```jsx
// OrbitControls: 카메라 컨트롤
import { OrbitControls } from "https://unpkg.com/three@0.108.0/examples/jsm/controls/OrbitControls.js";

const controls = new OrbitControls(camera, renderer.domElement);

// OrbitControls 옵션

// angle 범위 정하기
controls.minPolarAngle = Math.radians(20);   
controls.maxPolarAngle = Math.radians(120);

controls.target.set(0, 10, 0); 
controls.autoRotate = true;     // 자동회전
controls.autoRotateSpeed = 1;   // 속도조정 (default: 1)
controls.enablePan = false;     // enablePan: 키보드 조작이 안됨
controls.enableZoom = false;    // enableZoom: zoomOut이 안됨
controls.enableDamping = true;   // enableDamping: 움직임이 부드러워짐
```



## Group

### 🤍 `Group`

```jsx
const group = new THREE.Group();
group.add();        // 그룹화하고 싶은 요소들 넣기
scene.add(group);   // 씬에 그룹 넣기
```



## Lights

✔ [three.js light docs](https://threejs.org/docs/index.html#api/en/lights/Light)

```jsx
//직사광
const color = 0xffffff;  // 조명색
const intensity = 1.3;   // 빛의 세기
const light = new THREE.DirectionalLight(color, intensity); // 직사광
light.position.set(20, 80, 50);         // 조명 위치
light.target.position.set(0, 20, 0);    // 조명이 향하는 위치
scene.add(light);
scene.add(light.target);

// helper를 통해 빛의 움직임이 보임
// const helper = new THREE.DirectionalLightHelper(light, 5);
// scene.add(helper);
```



## Fog

```jsx
//안개
const near = 100;
const far = 300;
const color = "#000000";
scene.fog = new THREE.Fog(color, near, far);
```



## Raycaster

✔ [three.js Raycaster docs](https://threejs.org/docs/index.html?q=ray#api/en/core/Raycaster)

- 클릭 감지

```jsx
const raycaster = new THREE.Raycaster();
const pointer = new THREE.Vector2();

function onPointerMove( event ) {

	// calculate pointer position in normalized device coordinates
	// (-1 to +1) for both components

	pointer.x = ( event.clientX / window.innerWidth ) * 2 - 1;
	pointer.y = - ( event.clientY / window.innerHeight ) * 2 + 1;

}

function render() {

	// update the picking ray with the camera and pointer position
	raycaster.setFromCamera( pointer, camera );

	// calculate objects intersecting the picking ray
	const intersects = raycaster.intersectObjects( scene.children );

	for ( let i = 0; i < intersects.length; i ++ ) {

		intersects[ i ].object.material.color.set( 0xff0000 );

	}

	renderer.render( scene, camera );

}

window.addEventListener( 'pointermove', onPointerMove );

window.requestAnimationFrame(render);
```



## Examples

### 지구 만들기

```js
// 우주 공간 만들기
{
    const imageLoader = new THREE.TextureLoader();
    imageLoader.load("./image/universe.jpg", (data) => {
        
        // 로드한 우주이미지를 map으로 생성
        // 배경으로 사용하기 때문에 BackSide 설정
        const material_univ = new THREE.MeshBasicMaterial({
            map: data,
            side: THREE.BackSide,
        });
        const geometry_univ = new THREE.SphereGeometry(400, 32, 32);
        const universe = new THREE.Mesh(geometry_univ, material_univ);
        scene.add(universe);
    });
}

// 지구 만들기
{
    const imageLoader = new THREE.TextureLoader();
    
    imageLoader.load("./image/earth.jpg", (data) => {
        
        // 로드한 '지구이미지'를 map으로 생성
        const material_earth = new THREE.MeshBasicMaterial({
            map: data,
        });
        // (지)구 생성
        const geometry_earth = new THREE.SphereGeometry(80, 32, 32);
        const earth = new THREE.Mesh(geometry_earth, material_earth);
        scene.add(earth);
    });
}


const imageLoader = new THREE.TextureLoader();

// TextureLoader
imageLoader.load("./image/earth.jpg", (data) => {
    //지구 만들기 
    const geometry_earth = new THREE.SphereGeometry(80, 32, 32);
    const material_earth = new THREE.MeshBasicMaterial({
        map: data, // 로드한 지구이미지를 map으로 생성
    });
    const earth = new THREE.Mesh(geometry_earth, material_earth);
    scene.add(earth);
});
```



