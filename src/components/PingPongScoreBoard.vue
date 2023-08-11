<script setup>
const { score_1, score_2 } = defineProps([
  'score_1',
  'score_2',
  'incrementPlayerScore',
  'decrementPlayerScore',
  'resetScores',
])
</script>

<template>
  <div class="ping-pong-score-board">
    <table class="score-table">
      <tr>
        <th>Player 1</th>
        <th>Player 2</th>
      </tr>
      <tr>
        <td>{{ score_1 }}</td>
        <td>{{ score_2 }}</td>
      </tr>
    </table>

    <div class="button-container">
      <button class="button" @click="incrementPlayerScore(1)">
        Player 1 Score +
      </button>
      <button class="button" @click="decrementPlayerScore(1)">
        Player 1 Score -
      </button>
      <button class="button" @click="resetScores">Reset</button>
      <button class="button" @click="incrementPlayerScore(2)">
        Player 2 Score +
      </button>
      <button class="button" @click="decrementPlayerScore(2)">
        Player 2 Score -
      </button>
    </div>
  </div>
</template>
