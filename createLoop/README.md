# createLoop
createLoop provides a simple and structured library for creating animation loops in the browser. For p5 implementation see [p5.createLoop](https://www.npmjs.com/package/p5.createloop).

Features include:
- noise loops
- GIF image rendering

**Ye be warned** This is a baby library and has a lot of growing up to do. Breaking changes ahoy.
Loop animations can easily be created and rendered as a GIF:

```js
    const loop = createLoop({ 
        duration:5, 
        framesPerSecond:30,
        gif:true,
        canvas })
```

## Example

![vanilla simple example](examples/images/vanilla.gif)

html:
```html
    <script src="https://unpkg.com/createloop@latest/createloop.min.js.js"></script>
```

javascript:
```js
window.addEventListener('load', _ => {
    const canvas = document.createElement('canvas')
    document.body.appendChild(canvas)
    const ctx = canvas.getContext('2d')
    const loop = createLoop({ 
        duration:5, 
        framesPerSecond:30,
        gif:true,
        canvas })

    onAnimationFrame()

    function onAnimationFrame() {
        loop.preRender()
        //use this to match the canvas render speed to the rendered GIF speed
        if (loop.elapsedDeltaTime < loop.frameDeltaTime)
            render()
        requestAnimationFrame(onAnimationFrame)
    }

    function render() {
        const hw = canvas.width/2
        const hh = canvas.height/2
        const grd = ctx.createRadialGradient(hw, hh, 0, hw, hh, hh)
        //use loop.progress to match the hue to the progress through the loop
        grd.addColorStop(0, `hsl(${loop.progress * 360},100%,50%)`)
        grd.addColorStop(1, `white`)
        ctx.fillStyle = grd
        ctx.fillRect(0, 0, canvas.width, canvas.height)
        //post render must be called to add GIF frames
        loop.postRender()
    }
})

```

## Documentation

createLoop works to make creating GIF animations easy. For this reason it needs a concept of a `loop frame` occuring at some controlled frame rate. This is not to be confused with `requestAnimationFrame()`, which should be called without delay. 

Animations can be synced to the loop using the provided `progress` and `theta` properties.

Additionally, it is reccomended to throttle animation rendering using `elapsedDeltaTime` and `frameDeltaTime` as per the method in the example above. This will render the canvas at approximately the same frame rate as the GIF animation, giving an easy sense of what the GIF will look like.

The GIF encoder [gif.js](https://github.com/jnordberg/gif.js) uses web workers to render the GIF asynchronously. By default, the GIF will be rendered alongside the sketch and can be downloaded by clicking on it.

### createLoop()

This method in the global namespace will return a loop instance. It can receive servarl arguments:

| Name              | Default     | Description                                                                                                         |
| ----------------- | ----------- | ------------------------------------------------------------------------------------------------------------------- |
| `duration`        | `3`         | sets the duration in seconds of the loop.                                                                           |
| `framesPerSecond` | `30`        | create an image element and render the gif alongside the sketch                                                     |
| `noise`           | `undefined` | options to be passed to noise module. see [noise options](README.md#noise-options)                                  |
| `gif`             | `undefined` | options to be passed to GIF module. Will not create GIF if left undefined. see [gif options](README.md#gif-options) |

#### noise options

| Name        | Default         | Description                                                                                            |
| ----------- | --------------- | ------------------------------------------------------------------------------------------------------ |
| `frequency` | `1`             | The 'randomness' of the noise. Behind the scenes this sets the radius of a circle in a 2D noise field. |
| `seed`      | `Math.random()` | The seed of the noise field                                                                            |

#### gif options

| Name        | Default    | Description                                                                                                             |
| ----------- | ---------- | ----------------------------------------------------------------------------------------------------------------------- |
| `render`    | `true`     | create an image element and render the GIF to the webpage. Clicking on the image will begin downlaoding the GIF.        |
| `open`      | `false`    | open the gif image in a new tab or window                                                                               |
| `download`  | `false`    | download the gif automatically                                                                                          |
| `startLoop` | `0`        | loop index to begin recording the GIF                                                                                   |
| `endLoop`   | `1`        | loop index to end recording the GIF                                                                                     |
| `canvas`    | `<canvas>` | The canvas to render. By default this is the first instance of a canvas on the webpage                                  |
| `options`   | `{}`       | options to pass to gif.js encoder. see [gif.js documentation](https://github.com/jnordberg/gif.js#user-content-options) |


### Loop instance

The loop instance contains several helpful functions and properties for creating awesome animation loops.

#### Celebrity attributes

| Name               | Description                                                                                                                                                                                                                 |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `progress`         | stage of completion of the loop. this is `elapsedFrames / framesPerLoop` and has a range of `0 to 1`                                                                                                                        |
| `theta`            | progress around a circle with a a range of `0 to two pi`                                                                                                                                                                    |
| `frameDeltaTime`   | miliseconds between frames                                                                                                                                                                                                  |
| `elapsedDeltaTime` | time since last frame                                                                                                                                                                                                       |
| `noiseFrequency()` | set the noise frequency, also described as `radius`. see [noise options](README.md#noise-options)                                                                                                                           |
| `noiseSeed()`      | set the noise seed. Behind the scenes every time this called with a new seed value, a new instance of [simplex-noise](https://github.com/jwagner/simplex-noise.js) is created. see [noise options](README.md#noise-options) |
| `noise()`          | returns a noise value between -1 and 1. This found by querying the position `loop.theta` with radius `noiseFrequency` on a circle in a 2D simplex noise field                                                               |
| `noise1D(x)`       | Same as above also accepting an `x` value, providing a 1D line of noise for each frame                                                                                                                                      |
| `noise2D(x,y)`     | Same as above also accepting a `y` value, providing a 2D plane of noise                                                                                                                                                     |


#### Additional attributes

Less common but useful for some

| Name              | Description                                                                               |
| ----------------- | ----------------------------------------------------------------------------------------- |
| `framesPerSecond` | number of frames in a second                                                              |
| `framesPerLoop`   | number of frames in a loop, this is an integer value of `framesPerSecond * duration`      |
| `elapsedFrames`   | frames elapsed since loop start. This will wrap back to 0 when it reaches `framesPerLoop` |
| `elapsedLoops`    | loops elapsed                                                                             |
| `lastFrameTime`   | time stamp of last frame                                                                  |


### Dependencies / License


    createLoop provides a simple and structured library for creating animation loops in the browser.
    Copyright (C) 2019 Peter Hayman

    This program is free software: you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation, either version 3 of the License, or
    (at your option) any later version.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details.

    You should have received a copy of the GNU General Public License
    along with this program.  If not, see <http://www.gnu.org/licenses/>.

The following libraries are included in `createLoop`.

- GIF Encoder 
  - Creator:    Johan Nordberg
  - Version:    gif.js 0.2.0
  - Link:       https://github.com/jnordberg/gif.js
  - License:    Copyright 2013 Johan Nordberg [MIT License](./MIT-license.md)
- Simplex Noise
  - Creator:    Jonas Wagner 
  - Version:    simplex-noise 2.4.0 
  - Link:       https://github.com/jwagner/simplex-noise.js
  - License:    Copyright 2015 Jonas Wagner [MIT License](./MIT-license.md)

This library was developed using techniques described in The Coding Train Coding challenges:
 - [GIF loops](https://youtu.be/nBKwCCtWlUg)
 - [Polar Noise](https://youtu.be/ZI1dmHv3MeM)
 - [Noise GIF loops](https://youtu.be/c6K-wJQ77yQ)
 - [4D polar noise loops](https://youtu.be/3_0Ax95jIrk)

### Developer notes

- not absolutely happy with `preRender()` and `postRender()` system. preRender is called every animationFrame and postRender is called every loopFrame. On the plus side it works seamslessy with p5.