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
          context.fillStyle = grid[row][col] ? '#f84c34' : '#ffff';
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

    // if alive and has 1 or less neighbour cell dies
    // if alive and has more than 3 neighbours cell dies
    // if dead and has 3 neighbours, cell becomes alive
    const advanceGeneration = () => {
      // initializing a new grid and checking every cell for their neighbours
      const newGridGeneration: number[][] = [];
      for (let row = 0; row < rowsLen; row++) {
        newGridGeneration[row] = [];
        for (let col = 0; col < colsLen; col++) {
          const neighbours = countNeighbors(row, col)

          if (grid[row][col] === 1) {
            if (neighbours < 2 || neighbours > 3)
              newGridGeneration[row][col] = 0
            else 
              newGridGeneration[row][col] = 1
          } else {
            if (neighbours === 3)
              newGridGeneration[row][col] = 1
            else
              newGridGeneration[row][col] = 0
          }
        }
      }

      // at the end update the grid with the new generation and draw it again
      grid = newGridGeneration
      drawGrid()
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
    })

  </script>
  
  <main>
    <div>
      <button on:click={startGame}>Start Game</button>
      <button on:click={stopGame}>Stop Game</button>
    </div>
  
    <div class="canvas-container">
      <canvas bind:this={canvas} width={width} height={height} />
    </div>
  </main>
  

  <style>
    @import './app.css';
  </style>
  