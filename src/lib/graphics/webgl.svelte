<script>
  import { onMount } from 'svelte';

  import { hexToWebGLColor } from './webgl/UtilFunctions.js';
  import { setupBackground, drawBackground } from './webgl/Background.js';

  let canvas;
  let aspectRatio;

  onMount(() => {
    // -------------------------------------------------------------------------
    // SETUP CONTEXT
    // -------------------------------------------------------------------------

    const gl = canvas.getContext('webgl');
    if (!gl) {
      console.error('Unable to initialize WebGL.');
      return;
    }

    // -------------------------------------------------------------------------
    // INITIALIZATION
    // -------------------------------------------------------------------------

    function resizeCanvasToDisplaySize(canvas, multiplier = 1) {
        const width  = canvas.clientWidth * multiplier | 0;
        const height = canvas.clientHeight * multiplier | 0;

        if (canvas.width !== width || canvas.height !== height) {
            canvas.width = width;
            canvas.height = height;
            return true; // Indicates the canvas was resized
        }

        return false; // Indicates the canvas size was not changed
    }

    function resizeCanvas() {
        if (resizeCanvasToDisplaySize(canvas, window.devicePixelRatio)) {
            aspectRatio = canvas.width / canvas.height;
            gl.viewport(0, 0, canvas.width, canvas.height);
        }
    }

    window.addEventListener('resize', resizeCanvas);
    resizeCanvas(); // Initial resize

		const bg = setupBackground(gl);

		// const white = hexToWebGLColor(0xf0f0f0);
		// const black = hexToWebGLColor(0x232323);

    // -------------------------------------------------------------------------
    // RENDER LOOP
    // -------------------------------------------------------------------------

    function render() {
			// gl.clearColor(0, 0, 0, 0);
			gl.clear(gl.COLOR_BUFFER_BIT);

			drawBackground(
				gl,
				bg,
				performance.now(),
				aspectRatio
			);

			requestAnimationFrame(render);
    }

    requestAnimationFrame(render);

    // Cleanup
    return () => {
      // gl.deleteBuffer(vertexBuffer);
      // gl.deleteShader(vertexShader);
      // gl.deleteShader(fragmentShader);
      // gl.deleteProgram(program);
    };
  });
</script>

<canvas bind:this={canvas} class:geometry={true}></canvas>

<style>
.geometry {
  position: absolute;
  top: 0;
  left:0;
  width: 100%;
  height: 100%;
  display: block; /* Removes potential extra space below the canvas */
  padding: 0;
  margin: 0;
  border: none;
  z-index: -1;
}
</style>
