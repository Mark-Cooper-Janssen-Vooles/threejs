# Three.js

https://threejs-journey.com/lessons#

Three.js is a 3D JS library that enables devs to create 3D experiences for the web. It works with WebGL, but you can make it work with SVG and CSS but those two are limited.

Contents
- Basics
  - [What is WebGL and why use three.js](#what-is-webgl-and-why-use-threejs)
  - [Basic Scene](#basic-scene)
  - [Webpack](#webpack)

---
## Basics 

### What is WebGL and why use three.js

- Javascript API
- Renders triangles at a remarkable speed 
- Result can be drawn as a `<canvas>`
- Compatible with most modern browsers
- Uses the graphic processing unit (GPU)
  - CPU can do calculations really fast but one by one
  - The GPU is a little slower but can do thousands of parallel calculations
- Can be used for 2D (pixie js), but we will focus on 3D 

- To draw a 3D model, the idea is to draw many triangles at the right position and colorize them so that they look the way we want 

- The GPU will position all those points at once 
- Once the points are placed, the GPU will draw each visible pixel of those triangles 

- those instruction to place the points and draw the pixels are written in shaders
- we provide a bunch of info to those shaders like hte points positions, model transformations, the camera coordinates and things get positions and colorized the way we want 

- this is why native webGL is so hard, drawing a single triangle on the canvas would take at least 100 lines of code 

- Threejs is the most popular webGL library 

---
### Basic Scene

Firstly we need a scene
- like a container
- we put objects, models, lights, etc. in it 
- At some point we ask three.js to render that scene 
`const scene = new THREE.Scene()`

Objects 
- Can be many things 
- Primitive geometries
- Imported models
- Particles
- Lights 
- etc
- start with a simple red cube. 

To create an object we need to create a Mesh 
- combo of geometry (the shape) and a material (how it looks)
- start wutg a BoxGeometry and a MeshBasicMaterial
`const geometry = new THREE.BoxGeometry(1, 1, 1)` (1 is the size, a unit of whatever)
- multiple ways to express a color: `const material = new THREE.MeshBasicMaterial({ color: '#ff0000' })`
- Mesh is comprised of geometry and material 
````js
const geometry = new THREE.BoxGeometry(1, 1, 1)
const material = new THREE.MeshBasicMaterial({ color: '#ff0000' })
const mesh = new THREE.Mesh(geometry, material)
````

Need to add this to the scene 
`scene.add(mesh)`

We need to provide a camera to provide a point of view 
- serve as point of view when doing a render
- Can have multiple and switch between them 
- Different types
- We use PerspectiveCamera
  - need to provide 'field of view' in degrees, i.e. 75. small value you don't see much, large value you can see more on the sides (distortion happens)
  - he usually uses 45 degrees, maybe 55. 75 gives distortion but we can see if we're messing up
  - 2nd argument is 'aspet ratio': the width of the render divided by the height of the render 
    - we don't have render yet, but we can decide on a size now. 
    - create a sizes object containing temporary values 

Need to provide a renderer
- render the scene from the camera point of view 
- drawn into a canvas 
- a canvas is a HTML element in which you can draw stuff 
- Threejs uses webgl to draw the render inside this canvas 
- you can create it or let threejs do it 
````js
// Renderer 
const canvas = document.querySelector('canvas.webgl')
const renderer = new THREE.WebGLRenderer({
  canvas: canvas
})
renderer.setSize(sizes.width, sizes.height)
renderer.render(scene, camera) // at this point nothing is visible because we're inside the cube 
````

- we need to move the camera backward
  - to transform an object, we can use the following properties:
    - position
    - rotation
    - scale
  - we could have moved the cube forward too, but we will move the camera
- position property is also an object with x, y, z properties
  - threejs considers forward/backward axis to be z
  - y is up
  - x is like going right 

---
### Webpack

- Loading three.js with a `<script>` has limitations
- doesn't include some of the classes
- we need those classes
- we need to run a server to emulate a website and for security reasons 
- we are going to use a bundler 
  - a tool which you send js, css, html, images, typescript, sass, etc 
  - the bundler apply potential modifications and output a web-friendly "bundle"
  - can do more like local server, manage dependencies, improve compatibility, add modules support, optimise files, deploy, etc. 
- Webpack is our bundler of choice  
  - most popular
  - handle most of our needs
  - good documentation
  - good community
  - well maintained 
  - hard to configure 