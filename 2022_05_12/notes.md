## Computer graphics with JavaScript in the browser

### Using the [Pts](https://ptsjs.org/) library
- using a library will allow us to shortcut to more sophisticated graphics
- less time spent with scaffolding in our program to interact with the canvas

### Creating a new document to use the library
- we'll need some more changes to the basic [document](../2022_05_05/canvas.html) to work with this library
- inclusion of the library
  - using an additional script element to reference the library
```html
<script type="text/javascript" src="https://unpkg.com/pts@0.10.9/dist/pts.js"></script>
```
- using the canvas with Pts
  - we can make use of our existing canvas element inside the `<script>` element
  - but we'll interface with the canvas using Pts instead
```js
"use strict";
Pts.namespace(window); // this allows us to access library functions without a prefix
function draw() {
    let canvas = document.querySelector("canvas");
    let space = new CanvasSpace(canvas); // setup the library interface to the canvas element
    space.setup({
        bgcolor: "#fff"
    });
    let form = space.getForm();
    
    space.playOnce();
}
```
- the 3 core concepts of Pts are 'space', 'form', and 'point'
  - space is the canvas, form is the pencil, points are positions we create in space
  - the form makes the points in space visible to us

### Understanding [color](https://en.wikipedia.org/wiki/RGB_color_model) for graphics
  - rgb
  - color resolution (bits)
  - hex coding, value coding

### Basic [linear algebra](https://www2.seas.gwu.edu/~simhaweb/misc/linearalgebra/)
- The first 3 sections of this [chapter](https://math.hws.edu/graphicsbook/c2/index.html) are also a great resource
- This is an excess of information - a lot can be discovered and understood with limited math background, but going beyond does require some substantial mathematics
- Fortunately, the Pts library abstracts some of this away for us, so we can understand some things as we go
  - all while we create some sophisticated graphics

#### Fundamentals
- points
  - a point is a place within a dimension
  - usually this dimension is elaborated in context using coordinates 
    - i.e. a point [1, 2] implies a place in a 2-dimensional plan
  - we can illustrate this idea with Pts, but keep in mind the Pts `Pt` is more than a reference to place
    - it has a radius, color, etc
  - add the following just before `space.playOnce()`
```js
         // add everything to the space using a 'player'
         //  this is all within the 'arrow function'
         space.add(() => {
             // geometry datas
             let first_point = new Pt([200, 200]);
             // provide drawing detaills to the form
             form.fillOnly("#000").point( first_point, 4, "circle" ); 
         });
```
  - add some more points
- vectors 
  - difference between a point and a vector is..
  - vector is essentially the difference between two points: the separation
    - points are just places
  - the basis of the vector (separation relative to what?) lets us convey something about the space we're in
  - this concept will allow us to create shapes, paths, and lines in space
```js
         space.add(() => {
             let remote_point = space.center;
             // draw
             // describe the basis of our space and render
             form.stroke("#333", 8).line( [new Pt(), remote_point.$subtract(0, remote_point.y)] );
             form.stroke("#333", 8).line( [new Pt(), remote_point.$subtract(remote_point.x, 0)] );

             // describe the separation between our point and the origin
             form.stroke("#999", 8).line( [new Pt(), remote_point] );
         });
```
- angles
  - useful for understanding radial (along arcing path) displacement between points
  - and vectors of course
  - can use the 'arc' function to demonstrate
  - in Pts, angles are relative to the origin (0, 0)
```js
         space.add(() => {
             let remote_point = space.center;
             // draw
             form.stroke("#999", 8).line( [new Pt(), remote_point] );
             // create an arc demonstrating the full pi/4 radians
             form.strokeOnly("#000", 8).arc(new Pt(), remote_point.magnitude(), 0, remote_point.angle())
             // modify the angle relative to the 'x' axis (horizontal)
             form.stroke("#3f3", 8).line( [new Pt(), remote_point.toAngle(remote_point.angle() / 2)] );
             form.strokeOnly("#432", 8).arc(new Pt(), remote_point.magnitude(), 0, remote_point.angle())
         });
```
- transforms
  - with our basic grasp of vectors in space, we can use transforms to alter paths and points
```js
         space.add(() => {
             let point_group = new Group(space.center,
                                         new Pt(space.center).toAngle(space.center.angle() / 2));
             // draw
             form.stroke("#999", 8).line( [new Pt(), point_group[0]] );
             // create an arc demonstrating the full pi/4 radians
             form.strokeOnly("#000", 8).arc(new Pt(), point_group[0].magnitude(), 0, point_group[0].angle())
             // modify the angle relative to the 'x' axis (horizontal)
             form.stroke("#3f3", 8).line( [new Pt(), point_group[1]] );
             form.strokeOnly("#432", 8).arc(new Pt(), point_group[1].magnitude(), 0, point_group[1].angle())

             point_group.forEachPt("scale", 0.5)

             form.stroke("#298", 8).line( [new Pt(), point_group[0]] );
             form.strokeOnly("#907", 8).arc(new Pt(), point_group[0].magnitude(), 0, point_group[0].angle())
             form.stroke("#ff9", 8).line( [new Pt(), point_group[1]] );
             form.strokeOnly("#973", 8).arc(new Pt(), point_group[1].magnitude(), 0, point_group[1].angle())
         });

```
  - using the Pts `Group`, we can transform collections of points altogether
  - can we do this programmatically to make the code even simpler?

## Next time
- procedural / programmatic graphics generation
- animations
