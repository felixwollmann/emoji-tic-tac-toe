<script>
  let field = Array(3)
    .fill(null)
    .map(() => Array(3).fill(null));

  $: xHasWon =
    (field[0][0] === "X" && field[0][1] === "X" && field[0][2] === "X") ||
    (field[1][0] === "X" && field[1][1] === "X" && field[1][2] === "X") ||
    (field[2][0] === "X" && field[2][1] === "X" && field[2][2] === "X") ||
    (field[0][0] === "X" && field[1][0] === "X" && field[2][0] === "X") ||
    (field[0][1] === "X" && field[1][1] === "X" && field[2][1] === "X") ||
    (field[0][2] === "X" && field[1][2] === "X" && field[2][2] === "X") ||
    (field[0][0] === "X" && field[1][1] === "X" && field[2][2] === "X") ||
    (field[2][0] === "X" && field[1][1] === "X" && field[0][2] === "X");

  $: oHasWon =
    (field[0][0] === "O" && field[0][1] === "O" && field[0][2] === "O") ||
    (field[1][0] === "O" && field[1][1] === "O" && field[1][2] === "O") ||
    (field[2][0] === "O" && field[2][1] === "O" && field[2][2] === "O") ||
    (field[0][0] === "O" && field[1][0] === "O" && field[2][0] === "O") ||
    (field[0][1] === "O" && field[1][1] === "O" && field[2][1] === "O") ||
    (field[0][2] === "O" && field[1][2] === "O" && field[2][2] === "O") ||
    (field[0][0] === "O" && field[1][1] === "O" && field[2][2] === "O") ||
    (field[2][0] === "O" && field[1][1] === "O" && field[0][2] === "O");

  $: gameEnded =
    xHasWon ||
    oHasWon ||
    field.every((row) => row.every((cell) => cell !== null));

  let turn = "X";
  function handleClick(indexRow, indexCell) {
    if (turn == "X") {
      field[indexRow][indexCell] = "X";
      turn = "O";
    } else {
      field[indexRow][indexCell] = "O";
      turn = "X";
    }
  }

  $: if (gameEnded) turn = null;

  function resetGame() {
    field = Array(3)
      .fill(null)
      .map(() => Array(3).fill(null));
    turn = "X";
  }
</script>

<svelte:head>
  <link
    rel="stylesheet"
    href="https://cdnjs.cloudflare.com/ajax/libs/normalize/8.0.1/normalize.min.css"
    integrity="sha512-NhSC1YmyruXifcj/KFRWoC561YpHpc5Jtzgvbuzx5VozKpWvQ+4nXhPdFgmx8xqexRcpAglTj9sIBWINXa8x5w=="
    crossorigin="anonymous"
    referrerpolicy="no-referrer"
  />
</svelte:head>

<main class:x-turn={turn === "X"} class:o-turn={turn === "O"}>
  <div class="information">
    {#if gameEnded}
      🏁
      {#if xHasWon}
        🏆 ❌
      {:else if oHasWon}
        🏆 🔵
      {:else}
        🤷
      {/if}
      <div class="reset-button" on:click={resetGame}>🔄</div>
    {:else if turn == "X"}
      ❌
    {:else}
      🔵
    {/if}
  </div>
  <div class="game">
    {#each field as row, indexRow}
      <div class="row">
        {#each row as cell, indexCell}
          <div
            class="cell"
            class:cell-x={cell === "X"}
            class:cell-o={cell === "O"}
            class:playable={cell === null && !gameEnded}
            class:cell-empty={cell === null}
            on:click={cell != null || gameEnded
              ? null
              : () => handleClick(indexRow, indexCell)}
          >
            {cell || ""}
          </div>
        {/each}
      </div>
    {/each}
  </div>
</main>

<style>
  :global(:root) {
    --bg-color: white;
    --btn-color: hsl(0, 0%, 93%);
    --btn-color-hover: hsl(0, 0%, 80%);

    --no-turn-color: hsl(0, 0%, 70%);
    --light-red: hsl(0, 73%, 70%);
    --light-blue: hsl(240, 81%, 70%);

    --red: hsl(0, 73%, 36%);
    --blue: hsl(240, 81%, 36%);
  }

  @media (prefers-color-scheme: dark) {
    :global(:root) {
      --bg-color: black;
      --btn-color: hsl(0, 0%, 7%);
      --btn-color-hover: hsl(0, 0%, 20%);

      --no-turn-color: hsl(0, 0%, 30%);

      --light-red: hsl(0, 50%, 30%);
      --light-blue: hsl(240, 50%, 30%);

      --red: hsl(0, 73%, 64%);
      --blue: hsl(240, 81%, 64%);
    }
  }
  .row {
    display: flex;
    flex-direction: row;
  }

  .cell {
    height: 80px;
    width: 80px;
    background-color: var(--bg-color);
    /* padding: auto; */
    margin: 20px;

    display: flex;
    justify-content: center;
    align-items: center;

    color: white;

    border-radius: 10px;

    /* cursor: pointer; */

    font-size: xx-large;
    font-weight: 700;

    transition: background-color 0.2s ease-in-out;

    user-select: none;
  }

  .cell.playable {
    cursor: pointer;
  }

  .cell:not(.playable) {
    cursor: default;
  }

  .cell-x {
    background-color: var(--red);
  }

  .cell-o {
    background-color: var(--blue);
  }

  .reset-button {
    cursor: pointer;
    position: absolute;
    top: 0;
    right: 0;

    border-radius: inherit;
    /* border-top-left-radius: 0; */
    /* border-bottom-left-radius: 0; */

    height: 100%;
    aspect-ratio: 1/1;
    width: auto;
    background-color: var(--btn-color);

    display: flex;
    justify-content: center;
    align-items: center;

    transition: background-color 0.2s ease-in-out;
  }

  .reset-button:hover {
    background-color: var(--btn-color-hover);
  }

  div.information {
    background-color: var(--bg-color);
    padding: 10px;
    border-radius: 10px;

    position: relative;

    font-size: xx-large;
    user-select: none;

    margin-bottom: 1.7rem;
  }
  :global(:root) {
    font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;

    /* font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
      Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif; */
  }

  :global(html) {
    height: 100%;
    width: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    background-color: var(--bg-color);
  }

  main {
    text-align: center;
    padding: 3em;
    margin: 0 auto;
    background-color: var(--no-turn-color);
    border-radius: 1.5em;

    transition: background-color 0.2s ease-in-out;
  }

  main.x-turn {
    background-color: var(--light-red);
  }

  main.o-turn {
    background-color: var(--light-blue);
  }
</style>
