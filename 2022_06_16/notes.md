## Computer graphics with JavaScript in the browser

### animating with user input + control
- interested in an animation that responds to user interaction
- let's start with a simple line drawing
  - add the following to your html `<script>` tag, where you see the comment
```js
         space.add( (time, _) => {
            const size = 20;

            let lines = [...Array(size)].map((_, i) => {
                let x_index = i * Math.floor(space.width / size);
                return new Group(
                    new Pt(x_index, 0), new Pt(x_index, space.height)
                )
            });

             lines.forEach((line, idx) => {
                 form.strokeOnly('#000', 6).line(line)
             });
         });
```
- reload and see a series of evenly spaced lines
#### building the lines from
```js
            let lines = [...Array(size)].map((_, i) => {
                //
            });
```
- using `...Array`
  - the `Array` call creates a list of items
  - it 'spreads' them with `...`
- the `map` function
  - lets us apply a function to each item
  - we return a new item within our function
  - this item is accumulated along with every iteration over the list, to make a new one

### basic animation of the lines
- we'll add a calculation for the drift for each animation frame
- we'll use the x_index calculation to determine how many pixels to drift
- apply the drift on the x-axis with each new frame
```js
         space.add( (time, _) => {
            const size = 20;
            const change = Num.cycle( (time%4000)/4000 ); // calculate the change for this frame
            let offset = Math.floor(space.width / size); // how many pixels between each line
            let drift = Num.mapToRange(change, 0, 1, -offset/2, offset/2); // how many pixels to drift by

            let lines = [...Array(size)].map((_, i) => {
                let x_index = Math.floor(i * offset); // calculate the x_index using the offset
                return new Group(
                    new Pt(x_index + drift, 0), new Pt(x_index + drift, space.height) // apply the drift
                )
            });

             lines.forEach((line, idx) => {
                 form.strokeOnly('#000', 6).line(line)
             });
         });
```

### incorporating user control
- we can use the pointer to interact with the animation
- with the location of the pointer, we can respond depending on the coordinate position
  - as the pointer moves in the x direction, do that..
- two basic principles for doing this
  - the mouse pointer is provided with `space.pointer`
  - map the position to a value we can use
  - bind the animation to the mouse with `space.bindMouse().play()`
```js
        space.add( (time, _) => {
            const size = 20;
            const change = Num.cycle( (time%4000)/4000 );
            let offset = Math.floor(space.width / size);
            let drift = Num.mapToRange(change, 0, 1, -offset/2, offset/2);
            let y_ctrl = space.pointer.y / space.size.y // calculate the relative position of the pointer

            let lines = [...Array(size)].map((_, i) => {
                let x_index = Math.floor(i * offset);
                return new Group(
                    new Pt(x_index + drift, 0).scale(y_ctrl), 
                    new Pt(x_index + drift, space.height).scale(1 - y_ctrl) // scale the points using control
                )
            });

             lines.forEach((line, idx) => {
                 form.strokeOnly('#000', 6).line(line)
             });
         });
```

### timed / scheduled animations
- we can incorporate events that happen in tandem with our interactions
  - and ultimately, we can influence those events with user control

### tempo
- Pts offers a `tempo` functionality
- allows us to coordinate animations with time

### refactoring some code
- first, lets move the array declaration outside of the animation code
- we can use it to share information
  - between our tempo events and the cycling animation
  - put this after `let form = space.getForm();`
```js
        const size = 20;
        let line_colors = Array(size).fill("#000");
```
- and update the code from before to use the array
```js
            let lines = line_colors.map((_, i) => {
            ...
            });

            lines.forEach((line, idx) => {
                form.strokeOnly(line_colors[idx], 6).line(line);
            });
```

### create the tempo event
- add the following right after the `line_colors` array creation
```js
        let tempo = new Tempo(300); // 300 beats per minute tempo
        tempo.every(1).start((count) => { // do something every one beat of the tempo count
            line_colors[count % size] = "#2f3"; // update one of the line colors in the array
            line_colors[(count % size) - 1 < 0 ? size - 1 : (count % size) - 1] = "#000"; // backtrack and restore the last line color
        });

        space.add( tempo ); // add the event to the animation space
```

### follow-up ideas for improving the animation
- the x-coordinate can be used to add more expression
- adjust aspects of the tempo event with control
- make dynamic changes to color
  - update more line colors at a time

### complete animation code
```js
     function draw() {
        let canvas = document.querySelector("canvas");
        let space = new CanvasSpace(canvas);
        space.setup({
            bgcolor: "#fff"
        });
        let form = space.getForm();

        const size = 40;
        let base_color = Color.fromHex('#dbd8c3');

        let line_colors = Array(size).fill(base_color);

        function get_wrapped_index(count) {
            return (count % size) - 1 < 0 ? size - 1 : (count % size) - 1;
        }

        let tempo = new Tempo(300);
        tempo.every(1).start((count) => {
            line_colors[count % size] = Color.fromHex("#143");
            line_colors[get_wrapped_index(count)] = base_color;
        });

        tempo.every(2).start((count) => {
            line_colors[count % size] = Color.fromHex("#192");
            line_colors[get_wrapped_index(count)] = base_color;
        });

        tempo.every(6).start((count) => {
            line_colors[count % size] = Color.fromHex("#432");
            line_colors[get_wrapped_index(count)] = base_color;
        });

        space.add( tempo );

        space.add( (time, _) => {
            const change = Num.cycle( (time%4000)/4000 );
            let offset = Math.floor(space.width / size);
            let drift = Num.mapToRange(change, 0, 1, -offset/2, offset/2);
            let y_ctrl = space.pointer.y / space.size.y;

            let lines = line_colors.map((_, i) => {
                let x_index = Math.floor(i * offset);
                return new Group(
                    new Pt(x_index + drift, 0).rotate2D(1 - y_ctrl), 
                    new Pt(x_index + drift, space.height).rotate2D(y_ctrl)
                )
            });

             lines.forEach((line, idx) => {
                 form.strokeOnly(line_colors[idx].hex, 6).line(line)
             });

             let x_ctrl = space.pointer.x / space.size.x;
             base_color = Color.fromHex('#dbd8c3').multiply(x_ctrl);
         });

         space.bindMouse().play();
     }
```
