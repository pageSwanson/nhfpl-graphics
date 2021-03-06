## Computer Graphics with JavaScript in the browser

### Using [JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
- the language
  - a scripting language, which is [interpretted](https://en.wikipedia.org/wiki/Just-in-time_compilation)
  - follows specifications laid out by the [ECMA](https://stackoverflow.com/a/4269188) standards
  - where is JavaScript used? different scripts in different contexts..
- the context
  - for this series, alongside a document created with [HTML](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/HTML_basics)
  - this document is rendered using a browser..
- the [browser](https://en.wikipedia.org/wiki/Web_browser) (our JavaScript runtime)
  - the browser maintains an engine that dictates how to interpret the JavaScript
    - with respect to the document
    - this is how a lot of modern web pages come together
  - an environment that knows what JavaScript is and how to execute it

### Introduction to the Canvas API (using JavaScript)
- This is the way we'll draw things using JavaScript code
- An [API](https://en.wikipedia.org/wiki/API) is just a user interface.. but for code
  - you have your own code, and someone else has written code that you would like to use
  - if they create an API, you can use their code easily enough through the interface they make

#### the Canvas
- adding the element to the document
  - start with a new [document using html](./canvas.html)
  - a basic [template](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial/Basic_usage#a_skeleton_template) gets us started quickly
    - my version is modified for simplicity
  - notice the canvas tag `<canvas></canvas>`
    - this is where we'll draw things
- the line below is JavaScript code, and is used to reference the canvas in the document
```js
let canvas = document.querySelector('canvas');
```
- the subsequent line lets us access the `CanvasRenderingContext2D`
  - the interface that we'll use to draw things to the canvas
  - getting this lets us draw things with our code
```js
let context = canvas.getContext ? canvas.getContext('2d') : undefined;
```

#### conceptualizing a grid
- canvas is an x-y coordinate plane
  - we can visualize it easily enough by creating a grid (the first thing we'll draw)
  - we'll do this with some rectangles
- add what's below to the end of your `<script>` section
```js
function draw_grid() {
    // lines that start with '//' are comments
    // const are fixed values, we don't want them to change
    const step = 200;

    // each rectangle needs a starting coordinate (x, y, ...)
    context.rect(0, 0, step, step);
    // then it needs the dimensions (..., x-dimension, y-dimension)
    context.rect(step, 0, step, step);
    // this adds a shape to the context, which we can execute as soon as we want to draw
    context.rect(step + step, 0, step, step);

    // for this block, we want to adjust the y coordinate..
    context.rect(0, step, step, step);
    // notice the difference compared with the first part of the code?
    context.rect(step, step, step, step);
    // this part of the code sets up the 2nd row in the grid
    context.rect(step + step, step, step, step);

    // add the final row of the grid to the context
    context.rect(0, step + step, step, step);
    context.rect(step, step + step, step, step);
    context.rect(step + step, step + step, step, step);

    // set the color for our drawing
    context.strokeStyle = 'lightblue';
    // with all of the above context, perform the 'stroke'
    context.stroke();
}
```
- and use the code you've added by 'calling the function' before the end of the script section
```js
draw_grid();
```

#### coordinates, rectangles
- drawing the grid can help us understand the coordinate system
  - x is the direction from left to right, y is from top to bottom, starting at 0
  - the canvas is navigated by offsetting a coordinate position
- the rectangle is an easy shape to visualize in the Canvas
  - it requires an x,y coordinate for the starting point
  - it requires a width, which can offset in either direction (negative to go 'left')
  - and a height, negative to go 'up'
- `context.rect` is the 'function' we use that creates this shape in context
  - it modifies the 'path' of an imaginary pencil
  - offset the position of x and y, provide the dimensions, rinse and repeat

#### fills, strokes
- used to put the pencil to the canvas
  - 'render' the path given the state of context
  - anything that's been added to the context up to this point will be drawn
- [`context.fill`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/fill)
  - function used to fill (paint bucket) shapes in the context
  - just like `strokeStyle`, the Canvas API supports a `fillStyle`
  - since `fill` works on the inner definition of the path in context..
    - we can pair it with stroke to render bordered shapes
```js
    context.fillStyle = 'grey';
    context.fill();
```
- [`context.stroke`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/stroke)
  - function used to outline shapes that are created on the context
  - you can modify the `strokeStyle` property on the context to change the way this renders
```js
    // different stroke styles (colors)
    context.strokeStyle = 'rgb(42, 208, 21)';

    // using hexadecimal color coding
    context.strokeStyle = '#6FD013';

    // transparent color style
    context.strokeStyle = 'rgba(111, 208, 21, 0.2)';
```
  - you can change the way the grid is rendered by adding these before calling stroke;
  - keep in mind - you must call `context.beginPath()` each time you call stroke to refresh the context
- calling `context.fill` followed by `context.stroke` will let you observe the result of both, with the stroke overlapping the fill
- See this [document](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial/Applying_styles_and_colors) for more ways to style strokes and fills

## Next time
- using a programmatic approach to do more
- using a library to simplify our code, create more quickly
