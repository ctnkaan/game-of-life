<script lang="ts">
    import { onMount } from 'svelte';
  
    let canvas: HTMLCanvasElement;
    let context: CanvasRenderingContext2D;    
  

    // Grid settings
    const width = 800;
    const height = 800;
    const cellSize = 20;
    const rows = Math.floor(height / cellSize);
    const cols = Math.floor(width / cellSize);

    // Initialize the grid
    let grid: number[][] = [];

    const initializeGrid = () => {
      for (let row = 0; row < rows; row++) {
        grid[row] = [];
        for (let col = 0; col < cols; col++) {
          grid[row][col] = 0; // Initialize all cells as dead
        }
      }
    }

     const drawGrid = () => {
      for (let row = 0; row < rows; row++) {
        for (let col = 0; col < cols; col++) {
          context.beginPath()
          context.rect(col * cellSize, row * cellSize, cellSize, cellSize);
          context.fillStyle = grid[row][col] ? 'black' : 'white';
          context.fill()
          context.stroke()
        }
      }
    }

    // Function to handle canvas click
    const handleCanvasClick = (event: MouseEvent) => {
      const rect = canvas.getBoundingClientRect()
    
      // clientX - left gives position from left side
      // clientY - top gives position from top side
      const x = event.clientX - rect.left
      const y = event.clientY - rect.top

      // By getting the x and y we positions we can divide it
      // by cellSize to get the number of the cell likea a cordinate system
      const col = Math.floor(x / cellSize)
      const row = Math.floor(y / cellSize)

      // Toggle alive or dead
      grid[row][col] = grid[row][col] ? 0 : 1
      drawGrid()
    }
    
    onMount(() => {
      context = canvas.getContext('2d') as CanvasRenderingContext2D;
      initializeGrid()
      drawGrid()
      canvas.addEventListener('click', handleCanvasClick);
    });
  </script>
  
  <canvas bind:this={canvas} width={width} height={height}></canvas>
  
  <style>
    canvas {
      border: 1px solid black;
    }
  </style>
  