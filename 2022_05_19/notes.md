## Computer graphics with JavaScript in the browser

### programmatic approach to drawing
- leveraging JavaScript to reduce the implementation for our approach

#### using functions
- we can create functions to encapsulate portions of our code
- these will be processes that we are interested in repeating
  - or processes that we want to parametrize
  - meaning we'll adjust the behavior based on some input
- using the transform idea from last session
  - we'll work with the 'rotate2D' transform this time
  - first add a function to encapsulate the form code
  - this belongs outside of the `draw` function
```js
     function rotate_stroke_points(form, point_group, radians, color) {
         let from_point = new Pt(point_group[1]);
         point_group[1].rotate2D( radians );

         form.stroke(color, 6).line( point_group );
         form.strokeOnly(color, 8).arc(point_group[0], space.width / 2, from_point.angle(), point_group[1].angle());
     }
```
- parameters are used to characterize the way the function behaves
  - the `form` is needed to draw to the canvas
  - the `point_group` is needed to update the data and to reference for drawing
  - `radians` and `color` can be used to alter the function actions
- the function can be called repeatedly with changes to the parameters
  - this gets us the same behavior as copying, pasting, and changing the calls
```js
         space.add(() => {
             let point_group = new Group(space.center,
                                         new Pt(space.width, space.center.y));

             rotate_stroke_points(form, point_group, 0, "#afd");

             rotate_stroke_points(form, point_group, Math.PI * 3 / 4, "#af2");
         });
```
- concise way of creating elaborate graphics

#### using iteration
- use these techniques to procedurally generate more curves, starting from more points
  - we can automatically generate points
```js
         space.add(() => {
             let point_group = new Group(space.center,
                                         new Pt(space.width, space.center.y));

             let radians = new Array(20); // create an array, representing each increment clockwise
             radians.fill(2 * Math.PI / radians.length); // populate the array automatically with the step size in radians (rotational step size)

             radians.forEach((x, idx) => { // do the following for each of the segments in the array
                 let from_point = new Pt(point_group[1]); // copy the point along the circumference before rotating
                 point_group.rotate2D(x);
                 point_group.scale((radians.length - idx) / radians.length); // scale the 'vector' by some fraction after we rotate

                 form.stroke("#348", 6).line( point_group );
                 // create an arc spanning the rotation from the last vector to the updated vector (after scale / rotation)
                 form.strokeOnly("#000", 6).arc(space.center, point_group[1].$subtract(space.center).magnitude(), from_point.angle(), x, true);
             });
         });
```
- scaling the same point groups using functions
- altering the design across a series of points

### animation of these transforms
- if we want to go about animating graphics, we consider
  - time and [frames](https://en.wikipedia.org/wiki/Frame_rate)
  - iteration

#### modify the `space.add` call, change `space.playOnce()`
- add the `time` parameter, which tracks the current running time in milliseconds
  - by default, we've been creating an 'animation', but we've only been rendering a single frame..
  - we could use a second parameter, `ftime`, for the time between frames
- change `space.playOnce()` to `space.play()`
  - this means the animation will play indefinitely, not just for one frame
```js
         space.add((time, _) => {
             let point_group = new Group(space.center,
                                         new Pt(space.width, space.center.y));
             // continued
             ...
         });

         space.play();
```
- for now though, nothing happens
  - we're not actually changing the graphics based on the time parameter
#### Animating changes to the point group
- two ways of going about this

##### render continuously, updating on some interval
- `Num.cycle`, modulo, and `time`
```js
         space.add((time, _) => {
             let point_group = new Group(space.center,
                                         new Pt(space.width, space.center.y));

             let segment_ratio = Num.cycle( (time%1000)/1000 );

             let radians = new Array(Math.floor(segment_ratio * 20));
             radians.fill(2 * Math.PI / radians.length);

             radians.forEach((x, idx) => {
                 let from_point = new Pt(point_group[1]);
                 point_group.rotate2D(x);
                 point_group.scale((radians.length - idx) / radians.length);

                 form.stroke("#348", 6).line( point_group );
                 form.strokeOnly("#000", 6).arc(space.center, point_group[1].$subtract(space.center).magnitude(), from_point.angle(), x, false);
             });
         });

         space.play();
```

##### render a new segment for each 'frame'
- calculate a framerate, and thus when to update the graphic
- render the update when it's time to do so
- synchronize with the iterative pattern
```js

```

### animate color changes

## Next time
- more sophisticated animations
- animate with user input + control
- scaling animation osciallates in and away from mouse pointer
