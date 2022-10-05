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
  - [Cameras](#cameras)
    - [PerspectiveCamera](#perspectivecamera)
    - [OrthographicCamera](#OrthographicCamera)
    - [Custom Controls (cursor)](#custom-controls-cursor)
    - [Built-in Controls](#built-in-controls)
  - [Fullscreen and Resizing](#fullscreen-and-resizing)
    - [Fit in the viewport](#fit-in-the-viewport)
    - [Handle Resize](#handle-resize)
    - [Handle Pixel ratio](#handle-pixel-ratio)
    - [Handle Fullscreen](#handle-fullscreen)
  - [Geometries]
    - [What is a geometry?](#what-is-a-geometry)
    - [Built-in Geometries](#built-in-geometries)
    - [Box Example](#box-example)
    - [Creating your own](#creating-your-own-buffer-geometry)


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

https://www.npmjs.com/package/three

`npm install three`

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


#### Request Animation Frame

The purpose of `requestAnimationFrame` is to call the function provided on the next frame 
We are going to call the same function on each new frame

we are going to make a function, call it, and within that call `requestAnimationFrame` for the next function

````js
// Animations
const tick = () => {
    console.log('tick')

    window.requestAnimationFrame(tick) // we just pass the function, not call. js will call on next frame. (60 times a second)
}

tick()
````

You have a function being called on each frame indefinitely 
=> move the render.render(...) in it and increase the cube rotation

````js
const tick = () => {
    // update Objects
    mesh.rotation.y += 0.01 // rotates it slowly
    // mesh.position.y += 0.01 // this makes it move off the screen

    // Render 
    renderer.render(scene, camera) // we need to move the renderer into this function

    window.requestAnimationFrame(tick)
}
````

unfortunately the higher the framerate, the faster the rotation. some gaming PC's will have faster framerates. 
- to fix we can adapt to the frame rate. 
  - we need to know how much time it's been since the last tick
    - use `Date.now()` to get the current timestamp
  - below is one solution to make it rotate independent of the frame rate
````js
// Time
let time = Date.now()

// Animations
const tick = () => {
    // Time
    const currentTime = Date.now()
    const deltaTime = currentTime - time
    time = currentTime

    // update Objects
    mesh.rotation.y += 0.001 * deltaTime

    // Render 
    renderer.render(scene, camera)

    window.requestAnimationFrame(tick) // we just pass the function, not call. js will call on next frame. (60 times a second)
}

tick()
````
  - another solution is to use `Clock` (an in-built soltuion by Threejs)



We can also change it so that the camera is what moves:
````js
    camera.position.y = Math.sin(elapsedTime)
    camera.position.x = Math.cos(elapsedTime)
    camera.lookAt(mesh.position);
````

There is a clock method called `getDelta(...)` => do not use it

If you want to have more control, create tweens (transition from a to b), create timelines, etc. you can use a library like GSAP. "Green Sock Library"

`npm install --save gsap@3.5.1` 
`import gsap from 'gasp'`

We can also use gsap:
````js
gsap.to(mesh.position, { duration: 1, delay: 1, x: 2 })
gsap.to(mesh.position, { duration: 1, delay: 2, x: 0 })

// Animations
const tick = () => {
    // Render 
    renderer.render(scene, camera)
    window.requestAnimationFrame(tick)
}

tick()
````

Gsap does its own logic - so our tick function is very simple

Choosing the right solution? No right answer because it depends on the project and your preferences. More complex things probably need external library like gsap.


----
### Cameras

`Camera` is an abstract class, you're not supposed to use it directly. I.e. used by `PerspectiveCamera`
`ArrayCamera` renders the scene from multiple cameras on specific areas of the render. I.e. think about ps2 playing multiplayer on the same split screen
`StereoCamera` render the scene through two cameras that mimic the eyes to create a parallax effects. Used with devices like VR headset, red and blue glasses or cardboard 
`CubeCamera` do 6 renders, each one facing a different direction. Can render the surrounding for things like environment map, reflection or shadow map.
`OrthographicCamera` is used to render the scene without perspective i.e. arcade games 
`PerspectiveCamera` renders the scene with perspective. 

We are going to use OrthograpgicCamera and PerspectiveCamera. 


#### PerspectiveCamera
We haven't used all the possible parameters of PerspectiveCamera
- First one is Field of View
  - Vertical vision angle (i.e. we used 75). He recommends between 45 and 75. 
  - in degrees
  - also called fov
- 2nd param is the Aspect Ratio
  - width of render divided by the height of the render (the render being the size)
  - `(sizes.width / sizes.height)`
- 3rd and 4th params are called "Near" and "Far"
  - they correspond to how close and how far the camera can see 
  - any object or part of the object closer than near or further than far will not show up
  - using extreme examples like `0.0001` and `99999999` is not recommended because it causes 'z fighting'
  - he recommends `0.1 and 100`. by default its 1 and 1000.

````js
// Camera
const camera = new THREE.PerspectiveCamera(75, sizes.width / sizes.height, 1, 3.4641016151377544) // if we put this position as the far, we can see some of it getting cut off (because the camera is set not to see that far)
camera.position.x = 2
camera.position.y = 2
camera.position.z = 2
console.log(camera.position.length()) // this returns 3.4641... etc
camera.lookAt(mesh.position)
scene.add(camera)
````



#### OrthographicCamera
- Differs from perspectiveCamera by lack of perspective
- Objects are same size despite their distance from the camera.
- Parameters are:
  - left, right, top, and bottom, then near and far.
  - example values: `const camera = new THREE.OrthographicCamera(-1, 1, 1, -1, 0.1, 100)`
  - the cube looks flat - we're rendering a square into a rectangle canvas.
    - we need to use the canvas ratio (width by height)
    - this fixes it: `const camera = new THREE.OrthographicCamera(-1 * aspectRatio, 1 * aspectRatio, 1, -1, 0.1, 100)` (you need to multiple the horizontal with the aspect ratio)


#### Custom Controls (cursor)
We want to control the camera position with the mouse
````js
// native javascript to get the position of the cursor:

// Cursor
window.addEventListener('mousemove', (event) => {
    console.log(event.clientX)
})
````
- the above gives us the pixel value, better to adjust it to a value with an amplitude of 1. 
  - wants value to go from -0.5 to +0.5 
  ````js
  // Cursor
  const cursor = { 
      x: 0,
      y: 0
  }
  window.addEventListener('mousemove', (event) => {
      cursor.x = event.clientX / sizes.width - 0.5
      cursor.y = event.clientY / sizes.height - 0.5
  })
  ````
- Update the camera position in the tick function with the cursor coordinates 
  - we need to make the cursor.y negative because in threejs the Y is up and down, so we get strange inverted behavior. While in the browser coordinates the y axis is left to right. `cursor.y = - (event.clientY / sizes height - 0.5)`
  - we can then set the camera position in the renderer: 
  ````js
      // Update camera 
    camera.position.x = cursor.x * 3 // (the *3 increase the amplitude)
    camera.position.y = cursor.y * 3
  ````
- to force the camera to look at something, you can do
  ````js
      // Update camera 
    camera.position.x = cursor.x * 3
    camera.position.y = cursor.y * 3
    camera.lookAt(mesh.position)
  ````
- we cannot see the back of the cube. We can rotate our camera around the cube with math. 
````js
// i.e. something like this:
    camera.position.x = Math.sin(cursor.x * Math.PI * 2) * 3
    camera.position.z = Math.cos(cursor.x * Math.PI * 2) * 3
    camera.position.y = cursor.y * 5
    camera.lookAt(mesh.position)
````


#### Built-in Controls
- There are many 'controls' in the three.js documentation
- Device Oritentation Controls 
  - used for smartphone mostly. i.e. see things moving while you move your phones camera. 
  - ios stopped supporting device orientation 
- Fly Controls 
  - enable moving the camera like if you were on a spaceship. you can rotate on all 3 axes, go forward and go backward 
- First Person Controls
  - like Fly controls but with a fixed up axis. doesn't work like in "FPS" games
- Pointer lock controls 
  - uses pointer lock javascript API
  - very immersive, like a video game
  - makes the mouse disappear (he wouldn't use this one)
- Orbit Control
  - similar to the controls we made with more features
- Trackball Controls
  - like orbit controls without the vertical angle limit (i.e. if you have no floor or sky)
- Transform Controls
  - has nothing to do with the camera
- Drag Controls
  - nothing to do with the camera

We will only use ORbitControls but feel free to test the other classes

Instantiating Orbit Controls:
- Class cannot be accessed normally like `THREE.OrbitControls`, need to import it ... 
- `import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'`
- need to provide 2 elements, your camera and a dom element that serves as a reference to put mouse events on it.
````js
// Controls 
const controls = new OrbitControls(camera, canvas) // these work without stuff below.
controls.target.y = 2 // it will come in centralised unless you change the target
controls.update() // need to call this if you change target
````

Controls work good but we can add `Damping` which smooths the animation by adding some kind of acceleration and friction.
- To enable the damping, switch the enableDamping property to true 
````js
// Controls 
const controls = new OrbitControls(camera, canvas)
controls.enableDamping = true

const tick = () =>
{
    // updating Controls
    controls.update()

    // Render
    renderer.render(scene, camera)
    window.requestAnimationFrame(tick)
}

tick()

````


When to use built-in controls or custom controls?
If a custom control can do everything you need, just use that.


---
### Fullscreen and Resizing

#### Fit in the viewport 
The current canvas is 800 x 600px

Fit in the "viewport" => the content of the webpage you can see

````js
const sizes = {
    width: window.innerWidth,
    height: window.innerHeight
}
````

This works but there is a margin on the top and on the left and we can scroll - because of the browser default stylings.

We can fix this:
````js
.webgl {
  position: fixed;
  top: 0px;
  left: 0px;
  outline: none; /* you might see this as blue when dragging or dropping */
}

html, body {
  overflow: hidden; // on a mac for example you can stroll down further and it shows white space
}
````

#### Handle Resize

When changing the screen size we see white space etc. 
We can use native js to check the resize:
````js
const sizes = {
    width: window.innerWidth,
    height: window.innerHeight
}

window.addEventListener('resize', () => {
    // update sizes
    sizes.width = window.innerWidth
    sizes.height = window.innerHeight

    // update camera
    camera.aspect = sizes.width / sizes.height
    camera.updateProjectionMatrix()

    // update renderer
    renderer.setSize(sizes.width, sizes.height)
})
````

#### Handle Pixel Ratio

- Some might see a blurry render and stairs effect on the edges. If so, its because your screen has a pixel ratio greater than 1. 
- Pixel ratio corresponds to how many physical pixels you have on the screen for 1 pixel unit of the software part 
  - i.e. 1:1 gives you 1 pixel, if you have a pixel ratio of 2 you have 4 pixels to render, a pixel ratio of 3 you have 9 pixels to render, etc etc. 
- We need to handle this, otherwise we will have blurryness 
- To get pixel ratio we can use window.devicePixelRatio
- in the renderer section you can add this and it will fix the majority of issues:
````js
renderer.setPixelRatio(window.devicePixelRatio)
````
- if we're rendering on a pixel ratio of 2+, we don't want to render too many pixels or it will make it slow. So to counteract this we do: 
`renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))` => this will set it at the pixel ratio of the browser, unless its greater than 2 (then it will just render 2)
- He also puts this inside the resize event listner
  - if a user changes the browser tab and puts it in a different screen, this accounts for that. 


#### Handle Fullscreen

- if you want them to be able to go to fullscreen
- add support to a fullscreen mode by double clicking anywhere 
````js
window.addEventListener('dblclick', () => {
  if(!document.fullscreenElement) {
    canvas.requestFullscreen()
  } else {
    document.exitFullscreen()
  }
})
````
- to go to fullscreen mode use requestFullscreen() on the concerned element (our canvas)
- unfortunately this won't work on Safari 

---

### Geometries 

#### What is a geometry?

- We've used BoxGeometry so far
- A geometry is:
  - composed of verticies
  - Can be used for meshes but also for particles
  - Can store more data than the positions (UV coordinates, normals, colors or anything we want)

#### Built-in Geometries

- all of the following gemoerties inherit from BufferGeometry
- many built in methods like translate(...), rotateX(...), normalize() etc
- threejs.org/docs/#api/en/geometries
- By combining these, we can create complex shapes
  - however, if its too complex, its better that we use a 3d modeling software like Blender

#### Box Example
6 parameters
- width: size on the x axis
- height: size on the y axis
- depth: size on the z axis
- widthSegments: how many subdivisions in the x axis
- heightSegments: how many subdivisions in the y axis
- depthSegments: how many subdivisions in the z axis 

Subdivisions correspond to how many triangles should compose a face
- 1 = 2 triangles per face
- 2 = 9 triangles per face 
`const geometry = new THREE.BoxGeometry(1, 1, 1, 2, 2, 2)`
- problem is we cannot see these triangles

````js
// Object
const geometry = new THREE.BoxGeometry(1, 1, 1, 2, 2, 2) // adds 2 as subdivision
const material = new THREE.MeshBasicMaterial({ 
    color: 0xff0000,
    wireframe: true // allows us to see the wireframe
 })
const mesh = new THREE.Mesh(geometry, material)
scene.add(mesh)
````

#### Creating your own buffer Geometry 

- things get harder 
- before creating the geometry, we need to understand how to store buffer geometry data
- we are going to use Float32Array (native JS)
  - typed Array, i.e. can only store floats
  - fixed length
  - easier to handle for the computer

````js
const positionsArray = new Float32Array([
    0, 0, 0, // first vertice 
    0, 1, 0, // second vertice
    1, 0, 0 // third vertice
])
const positionsAttribute = new THREE.BufferAttribute(positionsArray, 3)

const geometry = new THREE.BufferGeometry()
geometry.setAttribute('position', positionsAttribute)
````

Example where we create 500 triangles:

````js
const geometry = new THREE.BufferGeometry()
const count = 500
const positionsArray = new Float32Array(count * 3 * 3)

for(let i = 0; i < count * 3 * 3; i++) {
    positionsArray[i] = (Math.random() - 0.5) * 3
}

const positionsAttribute = new THREE.BufferAttribute(positionsArray, 3)
geometry.setAttribute('position', positionsAttribute)

````

---