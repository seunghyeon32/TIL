# Three.js

[TOC]



## Renderer 

**필수적으로 넣어줘야 함 !!**

```jsx
const renderer = new THREE.WebGLRenderer();

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

[three.js](https://threejs.org/docs/?q=Orbit#examples/ko/controls/OrbitControls)

```jsx
// OrbitControls: 카메라 컨트롤
import { OrbitControls } from "<https://unpkg.com/three@0.108.0/examples/jsm/controls/OrbitControls.js>";

const controls = new OrbitControls(camera, renderer.domElement);

// OrbitControls 옵션
// enableDamping: 움직임이 부드러워짐
controls.enableDamping = true;
```

