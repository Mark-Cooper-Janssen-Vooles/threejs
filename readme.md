# Three.js

https://threejs-journey.com/lessons#

Three.js is a 3D JS library that enables devs to create 3D experiences for the web. It works with WebGL, but you can make it work with SVG and CSS but those two are limited.

Contents
- Basics
  - [What is WebGL and why use three.js](#what-is-webgl-and-why-use-threejs)
  - [Basic Scene](#basic-scene)
  - [Webpack](#webpack)
  - [Transform Objects](#transform-objects)
    - [Position Objects](#position-objects)
    - [Scale Objects](#scale-objects) 
    - [Rotate Objects](#rotate-objects)
      - Quaternion Objects
    - [Groups](#groups)
  - [Animations](#animations)

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

  `import * as THREE from 'three'`


---
### Transform Objects

- there are 4 properties to transform objects
  - position
  - scale
  - rotation
  - quaternion 
    - like a rotation 
- all classes that inherit from Object3D possess properties like PerspectiveCamera and Mesh, i.e. perspectiveCamera and Mesh will have positon / scale / rotation / quaternion 


The units of measurement are arbitrary 
- "1" could be anything - 1 centimeter, 1 meter, 1 kilometer. whatever makes sense for your application. 


Axis:
- z is forward/backward 
- y is up 
- x is like going right 

#### Position Objects

the positions need to be set before you call `renderer.render(scene, camera)`

Vector3: 
- `position` inherits from Vector3, which has many useful methods, i.e. you can get the nelgth of a vector
- `length()`
- `distanceTo`
- `Normalize`
- `set(x, y, z)`
````js
console.log(mesh.position.length()) // the length of the vector is the distance between the center of the scene and our objects position 

console.log(mesh.position.distanceTo(camera.position)) // the distance from the  mesh from the object you pass in (which is another Vector3, i.e. the camera)

mesh.position.normalize() // takes the vector length and reduces it so its one

mesh.position.set(0.7, -0.6, 1)
// above is the same as: 
// mesh.position.x = 0.7
// mesh.position.y = -0.6
// mesh.position.z = -2
````


Positioning things in space can be hard - one tool to help is called `AxesHelper` to display a colored line for each axis. 

````js
// Axis helper 
const axesHelper = new THREE.AxesHelper() // 1 unit by default. can give it an argument, i.e "3" and it will be 3x bigger.
scene.add(axesHelper)
````

red axes helper is positive x
green is positive y 
blue is positive z 

#### Scale Objects 

Scale also has x, y, z. 
````js
mesh.scale.x = 2
mesh.scale.y = 0.5
mesh.scale.z = 0.5

mesh.scale.set(2, 0.5, 0.5) // or can just use this.
````

#### Rotate Objects 

You can use `rotation` property or `quaternion` property. Updating one updates the other automatically. 

`rotation` has x, y, and z properties but isn't a `vector3`, its a `Euler`.

When change the x, y and z properties you can imagine putting a stick through your objects center in the axis's direction and then rotating that object on that stick. 

i.e. the up axis is Y. You could imagine a stick going from bottom to top, then the object would rotate around that. 
The right axis is Z. You could imagine rotating around that axis, i.e. like a planes propeller. 
The X axis is the forward axis, i.e. wheels of a car, would rotate that way. 

a full 180 degree (half a rotation) rotation is PI, i.e. 3.14159... you can use `Math.PI`.

````js
// Rotation 
mesh.rotation.y = 3
````

Becareful when you rotate on an axis, you might also rotate on other axis. 

the rotation goes by default in the x, y, and z order and you can get strange result like an axis not working anymore. This is called "gimbal lock". 

In a gimbal lock you cannot re-order. 
You can change this order by using the `reorder(...)` method. `object.rotation.reorder('yxz')`

he says "we can do without z" in rotation.

````js
// Rotation 
mesh.rotation.reorder('YXZ')
mesh.rotation.x = Math.PI * 0.25
mesh.rotation.y = Math.PI * 0.25

// the above is applied first the Y then the X rotation, despite the order below. 
````

You have to reorder first. 

`Euler` is easy to understand but this axis order can be problematic. This is why most engines and 3D softwares use Quaternion.

Quaternion also expresses a rotation, but in a more mathematical way. We will not cover quaternions in this lesson but that the quaternion updates when you change the rotation, and vice versa. 


You can tell any Object3D to have a `lookAt(...)` method which rotates the object so that its -z faces the target you provided. The target must be a Vector3

`camera.lookAt(new THREE.Vector3(0, -1, 0))`


#### Combining Transformations 
You can combine position, rotation (or quaternion), and scale in any order. 

probs easiest to use:
````js
mesh.position.set(0,0,0)
mesh.scale.set(0,0,0)
mesh.rotation.reorder('YXZ')
mesh.rotation.x = Math.PI * 0.25
mesh.rotation.y = Math.PI * 0.25
````

### Groups

You can put objects inside groups and use `position`, `rotation` and `scale` on those groups.
To do that, use the `Group` class

````js
const group = new THREE.Group()
group.position.set(0, 1, 0)
group.scale.y = 2
group.rotation.y = 1
// group.scale.set(0, 0, 0)
scene.add(group)

const cube1 = new THREE.Mesh(
    new THREE.BoxGeometry(1, 1, 1),
    new THREE.MeshBasicMaterial({color: 0xff0000})
)
group.add(cube1)

const cube2 = new THREE.Mesh(
    new THREE.BoxGeometry(1, 1, 1),
    new THREE.MeshBasicMaterial({color: 0x00ff00})
)
cube2.position.set(-2, 0, 0)
group.add(cube2)

const cube3 = new THREE.Mesh(
    new THREE.BoxGeometry(1, 1, 1),
    new THREE.MeshBasicMaterial({color: 0x0000ff})
)
cube3.position.set(2, 0, 0)
group.add(cube3)
````

---
### Animations

Animating is like doing stop motion 
- move the object 
- take a picture
- move the object a bit more 
- take a picture 
- etc 


most screens run at 60 frames per sections (FPS), but not always. your animation must look the same regardless of the framerate. 


we need to update objects and do a render on each frame. 
we are going to do that in a function and call this function with `window.requestAnimationFrame(...)`
