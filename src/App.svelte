<script>
  import Game2048 from './2048.svelte';

  // 사용 예시
  let exampleValue = 'Example';

  // Game2048 컴포넌트에 바인딩할 변수
  let currentScore = 0;

  function updateScore() {
    // 점수 업데이트 로직
    currentScore += 10;
  }
</script>

<main>
  <h1>2048</h1>
  <Game2048 bind:currentScore={currentScore} on:updateScore={updateScore} />
</main>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>
