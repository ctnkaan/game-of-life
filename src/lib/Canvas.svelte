<script lang="ts">
    import { onMount } from 'svelte';
  
    let canvas: HTMLCanvasElement;
    let context: CanvasRenderingContext2D;    
  
    let intervalId: number | null = null;

    // Grid settings
    const width = 800;
    const height = 800;
    const cellSize = 20;
    const rowsLen = Math.floor(height / cellSize);
    const colsLen = Math.floor(width / cellSize);

    // Initialize the grid
    let grid: number[][] = [];

    const initializeGrid = () => {
      for (let row = 0; row < rowsLen; row++) {
        grid[row] = [];
        for (let col = 0; col < colsLen; col++) {
          grid[row][col] = 0; // Initialize all cells as dead
        }
      }
    }

     const drawGrid = () => {
      for (let row = 0; row < rowsLen; row++) {
        for (let col = 0; col < colsLen; col++) {
          context.beginPath()
          // This looks scary but first two params are rectangles x,y position other two are its width and height
          context.rect(col * cellSize, row * cellSize, cellSize, cellSize);
          context.fillStyle = grid[row][col] ? 'black' : 'white';
          context.fill()
          context.stroke()
        }
      }
    }

    const handleCanvasClick = (event: MouseEvent) => {
      // get info about clicked rectangle
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

    const advanceGeneration = () => {

    }


    const countNeighbors = (row: number, col: number): number => {
      let count = 0;
      
      // We are doing -1 and +1 here to check their neighbours
      // ex: row -1 = left side of row
      for (let neighbourRow = row - 1; neighbourRow <= row + 1; neighbourRow++) {
        for (let neighborCol = col - 1; neighborCol <= col + 1; neighborCol++) {
          
          const isNotProvidedRowOrCol = !(neighbourRow === row && neighborCol === col)
          const isNeighbourRowInBounds = neighbourRow >= 0 && neighbourRow < rowsLen
          const isNeighbourColInBounds = neighborCol >= 0 && neighborCol < colsLen 
          
          if (isNeighbourRowInBounds && isNeighbourColInBounds && isNotProvidedRowOrCol) {
            count += grid[neighbourRow][neighborCol]
          }
        }
      }
      return count;
    }

    


    const startGame = () => {
      if (intervalId === null) 
        intervalId = setInterval(advanceGeneration, 300);
    }

    const stopGame = () => {
      if (intervalId !== null) {
        clearInterval(intervalId);
        intervalId = null;
      }
    }

    onMount(() => {
      context = canvas.getContext('2d') as CanvasRenderingContext2D;
      initializeGrid()
      drawGrid()
      canvas.addEventListener('click', handleCanvasClick);
    });
  </script>
  
  <canvas bind:this={canvas} width={width} height={height}></canvas>
  <button on:click="{startGame}">Start Game</button>
  <button on:click="{stopGame}">Stop Game</button>

  <style>
    canvas {
      border: 1px solid black;
    }
  </style>
  