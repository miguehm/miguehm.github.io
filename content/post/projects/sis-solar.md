---
title: Modelo de Sistema Solar con Three.js usando Node.js y Vite
author: Miguel Angel Hernández Moreno
date: 2022-10-25T14:23:19Z
image: sis-solar/rotate-earth.jpg
draft: true
categories:
    - Proyectos
tags: ["threejs"]
---

# Resumen
La creación de este tipo de gráficas en 3D es de mucha utilidad en diferentes áreas desde la investigación científica hasta en las artes visuales, sin embargo, desarrollar una solución a medida podría resultar engorroso.

Crear una solución eficiente, portable y sencilla es un problema para todas aquellas personas que desean crear gráficas de calidad y no quieren pasar por una curva de aprendizaje interminable. 

A continuación se describe paso a paso la creación de una simulación de un sistema solar utilizando threejs, entre otras herramientas, con la finalidad de ser el detonante para que cualquier persona desarrolle sus propios proyectos acorde a sus necesidades. 

Además de utilizar threejs, se utiliza otras herramientas como Nodejs, Vite y Git que han facilitado notablemente el desarrollo de este proyecto enfocado a la web, además de ser fácilmente escalable a otras plataformas.

# Introducción
Crear soluciones gráficas ha implicado invertir una cantidad de tiempo y/o dinero considerable, pero si se accedía a tal riesgo, los resultados no garantizan la posibilidad de compartir nuestro proyecto para que sea utilizado tanto en plataformas de escritorio como en las móviles.

Existen diferentes maneras de crear gráficos portables por computadora, una de ellas es utilizar la librería *OpenGL* desde cero, pero obtener resultados notables requiere de un conocimiento notable en el tema.

Threejs resuelve ese problema, consiste en una herramienta que nos permite crear geometrías eficientes y de fácil acceso, solo se necesita un navegador para interactuar con el contenido creado en base a threejs, además, en combinación con:

- Nodejs: mantiene los módulos del proyecto actualizados y despliega un servidor de pruebas.
- Vite: facilita la detección de errores y optimiza la versión final.
- Git: controla todas las versiones de nuestra solución.

La calidad de los resultados crece exponencialmente de acuerdo al tiempo dedicado para aprender a utilizarla.
La contribución de este reporte es proponer un "flujo de trabajo" para facilitar el desarrollo de cualquier propuesta gráfica, en este caso, la simulación de un sistema solar.

# Diseño
> Este proyecto se escribió utilizando *Windows Subsystem for Linux* corriendo Ubuntu 20.04 y *Termux* desde Android.

## Herramientas de desarrollo

### Nodejs
Es una herramienta enfocada a servidores, pero también nos proporciona un fácil acceso a las librerías actualizadas de Threejs o cualquier biblioteca escrita en javascript, en caso de actualizar el proyecto a una nueva versión, con unos simples comandos el proyecto se mantiene al día:

Instalar threejs con el gestor de paquetes de Nodejs
```bash
$ npm install three
```

Actualizar las bibliotecas a la nueva versión
```bash
$ node update
```

Además nos provee con un servidor listo para correr el proyecto (alternativa a `python3 -m http.server`), sin embargo, para ejecutarlo utilizaremos otra herramienta.

### Vite
Es una herramienta que brinda una experiencia de de desarrollo mas ágil, cuenta con un servidor que se actualiza en tiempo real acorde a como guardemos nuestro proyecto y de un comando de compilación que genera archivos estáticos altamente optimizados para subirse al servidor final.

Otra característica de Vite es que mejora la experiencia de trabajar con javascript de forma *modular*, esto es, disminuyendo el tiempo de carga respecto a los ES modules nativos entre otros beneficios.

En este proyecto, Vite sirve para crear el servidor local donde se prueba nuestro codigo y a *compilar* nuestro proyecto para subirlo a un servidor público.

Instalar Vite
```bash
$ npm install vite 
```

Iniciar un proyecto nuevo
```bash
$ npm init vite
```

Correr proyecto en un servidor
```bash
$ npm run dev
```

Exportar proyecto para producción
```bash
$ npm run build
```

### Git
Controlar las versiones de este proyecto resulta ágil con git ya que podemos rastrear los cambios realizados a lo largo del tiempo, hacer pruebas con seguridad y trabajar desde cualquier maquina con nuestro repositorio remoto en Github.

## Agregando Geometrías
Se utilizan tanto primitivas como importadas desde Blender.

### Agujero negro
Este elemento se trata de un objeto creado en Blender en formato GLTF el cual Threejs puede cargar mas eficientemente.

![Agujero negro creado en Blender](sis-solar/blackhole_model.jpg)

Para lograr esto, se debe importar la librería `GLTFLoader` desde los loaders predeterminados de Threejs.

```javascript
const path = 'three/examples/jsm/loaders/GLTFLoader';
import {GLTFLoader} from path;

let blackhole; // para posterior controlar su posición
let blackholeLoader = new GLTFLoader();
blackholeLoader.load('blackhole.glb', function (gltf) {
	blackhole = gltf.scene;
	blackhole.scale.set(10, 10, 10);
	scene.add(blackhole);
}, undefined, function (error){
	console.log('No se cargó el modelo 3D');
	console.log(error);
});
```

### Planeta tierra
Se trata de una geometría tipo `SphereGeometry` que simula a la tierra, también se le carga una textura.

```javascript
const earth = new THREE.Mesh(
	new THREE.SphereGeometry(5, 15, 15),
	new THREE.MeshLambertMaterial({
		map: earthTexture
	})
);
earth.receiveShadow = true;
scene.add(earth);
```

### Luna
Para este satélite se emplea una `torusknotGeometry`.

```javascript
const torusKnot = new THREE.Mesh(
	new THREE.TorusKnotGeometry(2, 0.5, 20, 5),
	new THREE.MeshLambertMaterial({color: 0xf0f0f0})
);
torusKnot.receiveShadow = true;
scene.add(torusKnot);
```

## Luz
Para simular mas la naturaleza de una luz proveniente de los restos calientes que rotan alrededor de un agujero negro, se emplea una luz blanca del tipo `pointLight`.

```javascript
const pointLight = new THREE.PointLight(0xffffff);
scene.add(pointLight);
```

## Fondo
El fondo se comprende de una esfera la cual está texturizada en su interior para dar la impresión de estar en el espacio.

```javascript
const bgScene = new THREE.Mesh(
	new THREE.SphereGeometry(320, 20, 20),
	new THREE.MeshBasicMaterial({
		map: (new THREE.TextureLoader)
			.load('space.jpg'),
		side: THREE.DoubleSide
	})
);
scene.add(bgScene);
```

## Música
Agregar una melodía al proyecto requiere tanto cargarla en el HTML como crear un objeto del tipo `listener` para poder escucharla.

En el HTML.
```html
<audio id="stay" preload="auto">
	<source src="STAY.mp3">
</audio>
```

En el Javascript.
```javascript
const listener = new THREE.AudioListener();
camera.add(listener);

const sound1 = new THREE.Audio(listener);
const stay = document.getElementById('stay');
sound1.setMediaElementSource(stay);
sound1.setVolume(0.5);
stay.play();
```

## Cámara
Para lograr una mejor interacción con la escena, se utiliza la librería `PointerLockControls`.

```javascript
const path = '../PointerLockControls';
import {PointerLockControls} from path;
let controls = new PointerLockControls(
	camera, document.body
);
scene.add(controls.getObject());
```

## Modos de Cámara
Estos modos muestran diferentes perspectivas de la escena.

### Rotando alrededor de la Tierra
La cámara rota con respecto a la tierra mirando hacia el agujero negro. Se emplea la misma trayectoria de rotación que utiliza la luna.

![La cámara rota alrededor de la tierra](sis-solar/rotate-earth.jpg)

```javascript
camera.position.x = 60*Math.sin(stepEarth)+20;
camera.position.z = 60*Math.cos(stepEarth)+20;
camera.position.y = 5;
camera.lookAt(0, 0, 0);
```

### Vista superior
Nos proporciona una perspectiva de ángulo *picado*.

![Cámara en ángulo picado](sis-solar/superior-bh.jpg)

```javascript
camera.position.x = 0;
camera.position.z = 0;
camera.position.y = 60;
camera.lookAt(0, 0, 0);
```

### Vista lateral
Vemos los elementos desde uno de los lados de nuestro agujero negro.

![Punto de vista lateral](sis-solar/lateral.jpg)

```javascript
camera.position.x = 80;
camera.position.z = 0;
camera.position.y = 5;
camera.lookAt(0, 0, 0);
```

### Vista hacia la Tierra
Similar a *Vista lateral*, pero ahora la cámara enfoca a la tierra en todo momento.

![Mirando hacia la Tierra desde un lateral](sis-solar/pov_tierra.jpg)

```javascript
camera.position.x = 53;
camera.position.z = 0;
camera.position.y = 5;
camera.lookAt(earth.position);
```

### Vista orbital con respecto al agujero negro
La cámara recorre la misma órbita de la tierra mirando hacia la misma. Como en el caso de *Rotando alrededor de la Tierra*, se emplean las mismas operaciones para rotar alrededor del agujero negro.

![Orbitando junto a la Tierra](sis-solar/orbital.jpg)

```javascript
camera.position.x = 75*Math.sin(stepEarth+0.6);
camera.position.z = 75*Math.cos(stepEarth+0.6);
camera.position.y = 10;
camera.lookAt(earth.position);
```

### Modo libre
En este modo, nos podemos mover hacia cualquier dirección empleando el ratón, las flechas para subir y bajar y la tecla espaciadora para avanzar frontalmente.

![Movimiento libre](sis-solar/libre.jpg)

# Evaluación
La ejecución comienza con el modo *Rotando alrededor de la Tierra* intercalando entre modos usando la tecla `M`.

![Primera vista al iniciar el proyecto](sis-solar/rotate-earth.jpg)

Emplear las herramientas anteriormente descritas incrementaron notablemente el proceso de codificación y rastreo de cambios.

![Registro de cambios en git](sis-solar/gitlog.jpg)

![Corriendo el servidor a tiempo real con vite](sis-solar/comando-vite.jpg)

Además, el rendimiento en navegador resulta ser aceptable para cualquier equipo de gama media.

![Consumo de procesador y memoria ram](sis-solar/consumo-memoria.jpg)

*Compilar* el proyecto nos ayuda a transformar nuestro proyecto a estático para subir solo lo necesario en nuestro servidor remoto.

![Archivos listos para montar en un servidor remoto](sis-solar/build-files.jpg)

# Conclusión
Se presenta un flujo de trabajo bajo ciertas herramientas que hacen mas sencillo programar con la librería Threejs. La simulación del Sistema Solar demuestra lo ágil y fácil de emplear cualquier librería o recurso bajo las normas que establece cada programa.

# Referencias
- [¿Que es Vite?](https://devjaime.medium.com/que-es-vite-y-como-funciona-m%C3%A1s-rapido-que-webpack-132f45efa4e4)
- [Misc controls - PointerLock](https://threejs.org/examples/#misc_controls_pointerlock)
- [Threejs Docs](https://threejs.org/docs/)
- [Blackhole model by Kenny Maguire Studio](https://skfb.ly/owSFQ)
