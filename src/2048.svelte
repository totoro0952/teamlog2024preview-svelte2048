<script>
  import { onMount } from "svelte";

  let board = Array(4)
    .fill()
    .map(() => Array(4).fill(0));
  let gameOver = false;

  onMount(() => {
    initializeBoard();
    addTile();
  });

  function initializeBoard() {
    board = Array(4)
      .fill()
      .map(() => Array(4).fill(0));
    gameOver = false;
  }

  function addTile() {
    const emptyTiles = [];
    for (let i = 0; i < 4; i++) {
      for (let j = 0; j < 4; j++) {
        if (board[i][j] === 0) {
          emptyTiles.push({ i, j });
        }
      }
    }

    if (emptyTiles.length > 0) {
      const { i, j } =
        emptyTiles[Math.floor(Math.random() * emptyTiles.length)];
      board[i][j] = Math.random() < 0.9 ? 2 : 4;
    } else {
      // 보드가 가득 찼고 더 이상 움직일 수 없는 경우
      if (!canMove()) {
        gameOver = true;
      }
    }
  }

  function handleKeyPress(event) {
    if (gameOver) {
      alert("Finish");
      return;
    }

    let moved = false;

    if (event.key === "ArrowUp" || event.key === "w") {
      moved = moveTiles("up");
    } else if (event.key === "ArrowDown" || event.key === "s") {
      moved = moveTiles("down");
    } else if (event.key === "ArrowLeft" || event.key === "a") {
      moved = moveTiles("left");
    } else if (event.key === "ArrowRight" || event.key === "d") {
      moved = moveTiles("right");
    }

    if (moved) {
      addTile();
    }
  }

  function moveTiles(direction) {
    let moved = false;

    for (let i = 0; i < 4; i++) {
      for (let j = 0; j < 4; j++) {
        if (board[i][j] !== 0) {
          if (direction === "up") {
            moved = moveTile(i, j, -1, 0) || moved;
          } else if (direction === "down") {
            moved = moveTile(i, j, 1, 0) || moved;
          } else if (direction === "left") {
            moved = moveTile(i, j, 0, -1) || moved;
          } else if (direction === "right") {
            moved = moveTile(i, j, 0, 1) || moved;
          }
        }
      }
    }

    // 더 이상 움직일 수 없을 때
    if (!canMove()) {
      gameOver = true;
    }

    return moved;
  }

  function moveTile(row, col, rowDirection, colDirection) {
    let moved = false;

    while (
      isValidPosition(row + rowDirection, col + colDirection) &&
      board[row + rowDirection][col + colDirection] === 0
    ) {
      board[row + rowDirection][col + colDirection] = board[row][col];
      board[row][col] = 0;
      row += rowDirection;
      col += colDirection;
      moved = true;
    }

    if (
      isValidPosition(row + rowDirection, col + colDirection) &&
      board[row + rowDirection][col + colDirection] === board[row][col]
    ) {
      board[row + rowDirection][col + colDirection] *= 2;
      board[row][col] = 0;
      moved = true;
    }

    return moved;
  }

  function isValidPosition(row, col) {
    return row >= 0 && row < 4 && col >= 0 && col < 4;
  }

  function canMove() {
    // 더 이상 움직일 수 있는지 확인
    for (let i = 0; i < 4; i++) {
      for (let j = 0; j < 4; j++) {
        if (
          (isValidPosition(i - 1, j) && board[i][j] === board[i - 1][j]) ||
          (isValidPosition(i + 1, j) && board[i][j] === board[i + 1][j]) ||
          (isValidPosition(i, j - 1) && board[i][j] === board[i][j - 1]) ||
          (isValidPosition(i, j + 1) && board[i][j] === board[i][j + 1])
        ) {
          return true; 
        }
      }
    }
    return false; 
  }

  function getCellStyle(value) {
    const colors = {
      0: "#ccc",
      2: "#eee",
      4: "#dcdc9a",
      8: "#f2f088",
      16: "#f7c576",
      32: "#f8a365",
      64: "#f98c5e",
      128: "#fa7357",
      256: "#fb5a50",
      512: "#fc4150",
      1024: "#fd2a4f",
      2048: "#ff144f",
    };

    const textColors = {
      2: "#000",
      4: "#000",
      8: "#000",
      16: "#000",
      32: "#000",
      64: "#000",
      128: "#fff",
      256: "#fff",
      512: "#fff",
      1024: "#fff",
      2048: "#fff",
    };

    return {
      backgroundColor: colors[value] || "#ff144f",
      color: textColors[value] || "#000",
    };
  }
</script>

<!-- svelte-ignore a11y-no-noninteractive-tabindex -->
<main on:keydown={handleKeyPress} tabindex="0">
  {#if gameOver}
    <div class="finish">Finish</div>
  {/if}
  {#each board as row, i}
    {#each row as cell, j}
      <div class="cell" bind:this={board[i][j]} style={getCellStyle(cell)}>
        {cell}
      </div>
    {/each}
  {/each}
</main>

<style>
  main {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    border-color: #000000;
    background-color: #ececec;
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    grid-template-rows: repeat(4, 1fr);
    gap: 10px;
    width: 440px;
    height: 440px;
  }

  .cell {
    width: 100px;
    height: 100px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 24px;
    font-weight: bold;
    border-radius: 10px;
  }

  .finish {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    font-size: 40px;
    font-weight: bold;
    color: #ff144f;
  }
</style>
