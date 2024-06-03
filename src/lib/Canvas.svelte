<script lang="ts">
    import { onMount } from 'svelte';
  
    let canvas: HTMLCanvasElement;
    let context: CanvasRenderingContext2D;    
  

    // Grid settings
    const width = 800;
    const height = 600;
    const cellSize = 20;
    const rows = Math.floor(height / cellSize);
    const cols = Math.floor(width / cellSize);
    
    // Function to draw the grid
    function drawGrid() {
      for (let col = 0; col <= cols; col++) {
        const x = col * cellSize
        context.beginPath()
        context.moveTo(x, 0)
        context.lineTo(x, height)
        context.stroke()
      }

      for (let row = 0; row <= rows; row++) {
        const y = row * cellSize
        context.beginPath()
        context.moveTo(0, y)
        context.lineTo(width, y)
        context.stroke()
      }

  }
    
    onMount(() => {
      context = canvas.getContext('2d') as CanvasRenderingContext2D;
      drawGrid()
    });
  </script>
  
  <canvas bind:this={canvas} width={width} height={height}></canvas>
  
  <style>
    canvas {
      border: 1px solid black;
    }
  </style>
  