<template>
  <div id="app">
    <PingPongScoreBoard
      :score_1="score_1"
      :score_2="score_2"
      :incrementPlayerScore="incrementPlayerScore"
      :decrementPlayerScore="decrementPlayerScore"
      :resetScores="resetScores"
    />
  </div>
</template>

<script setup>
import annyang from 'annyang'
import { ref, onMounted } from 'vue'

import PingPongScoreBoard from './components/PingPongScoreBoard.vue'

// Initial player scores
const score_1 = ref(0)
const score_2 = ref(0)
const turn = ref(1)
const play = ref(0)

const reset = [
  '점수 초기화',
  '초기화',
  '초기화해',
  '초기화해줘',
  '다시 시작',
  '다시시작',
  '다시 시작해',
  '다시시작해',
  '다시 시작해줘',
  '다시시작해줘'
]
const score = [
  '플레이어 *player 점수',
  '*player 점수',
  '플레이어 *player 점수 올려',
  '*player 점수 올려',
  '플레이어 *player 점수 올려줘',
  '*player 점수 올려줘',
  '*player 승리',
  '플레이어 *player 승리',
  '*player 이김',
  '플레이어 *player 이김'
]

function changeTurn() {
  play.value++

  /*
  1,2 --> 1
  3,4 --> 2
  5,6 --> 1
  7,8 --> 2
  9,10 --> 1
  */

  if (play.value % 2 === 0) {
    turn.value = (play.value / 2) % 2
  } else {
    turn.value = ((play.value + 1) / 2) % 2
  }
}

function incrementPlayerScore(player) {
  if (player === 1 || player === '일' || player === '원') {
    score_1.value++
  } else {
    score_2.value++
  }

  changeTurn()
  handleSpeaking()
}

function decrementPlayerScore(player) {
  if (player === 1 && score_1.value > 0) {
    score_1.value--
  } else if (player === 2 && score_2.value > 0) {
    score_2.value--
  }

  turn.value--
}

function speak(text) {
  try {
    const utterance = new SpeechSynthesisUtterance(text)
    utterance.lang = 'en-US'
    speechSynthesis.speak(utterance)
  } catch (e) {
    alert(e)
  }
}

onMounted(() => {
  if (annyang) {
    const commands = {
      이점수: () => incrementPlayerScore(2),
      '이 점수': () => incrementPlayerScore(2)
    }

    reset.forEach(command => (commands[command] = resetScores))
    score.forEach(
      command => (commands[command] = player => incrementPlayerScore(player))
    )

    annyang.setLanguage('ko-KR')
    annyang.addCommands(commands)

    annyang.addCallback('error', function () {
      console.log('error')
    })

    annyang.addCallback(
      'resultMatch',
      function (userSaid, commandText, phrases) {
        console.log(userSaid) // sample output: 'hello'
        console.log(commandText) // sample output: 'hello (there)'
        console.log(phrases) // sample output: ['hello', 'halo', 'yellow', 'polo', 'hello kitty']
      }
    )

    annyang.start()
  } else {
    console.error('annyang is not supported in this browser.')
  }
})

// Reset both players' scores
function resetScores() {
  score_1.value = 0
  score_2.value = 0

  speak('The score is reset')
}

async function handleSpeaking() {
  await setTimeout(() => {}, 100)

  speak(`${score_1.value} to ${score_2.value}`)

  if (score_1.value > score_2.value) {
    speak('Player one is winning.')
  } else if (score_1.value < score_2.value) {
    speak('Player two is winning.')
  } else {
    speak('The game is tied.')
  }

  if (score_1.value === 11 && score_2.value < 10) {
    speak('Player one won the game. Player two lose the game.')
  } else if (score_2.value === 11 && score_1.value < 10) {
    speak('Player two won the game. Player one lose the game.')
  } else if (score_1.value >= 10 && score_2.value >= 10) {
    if (score_1.value - score_2.value >= 2) {
      speak(
        'Player one won the game. Player two lose the game. It was a very long match.'
      )
    } else if (score_2.value - score_1.value >= 2) {
      speak(
        'Player two won the game. Player one lose the game. It was a very long match.'
      )
    }
  }

  if (score_1.value === score_2.value && score_1.value >= 10) {
    speak('Deuce')
  }

  speak(`player ${turn.value} should serve`)
}
</script>

<style>
#app {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.ping-pong-score-board {
  display: flex;
  flex-direction: column;
  align-items: center;
  font-size: 24px;
  border: 2px solid #333;
  border-radius: 10px;
  padding: 20px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.score-table {
  border-collapse: collapse;
  width: 100%;
  text-align: center;
  margin-bottom: 20px;
}

.score-table th {
  padding: 10px 0;
  font-weight: bold;
  background-color: #333;
  color: #fff;
}

.score-table td {
  padding: 10px 0;
  border-top: 1px solid #ddd;
}

@media (max-width: 600px) {
  #app {
    display: block;
    padding: 0;
  }

  td {
    font-size: 300px;
  }
}

.score {
  margin: 10px;
  display: flex;
  align-items: center;
}

.player-label {
  flex: 1;
}

.score-value {
  flex: 1;
  text-align: center;
}

.button-container {
  display: flex;
  justify-content: center;
  margin-top: 20px;
}

.button {
  padding: 10px 20px;
  margin: 5px;
  font-size: 18px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.button:active {
  background-color: #0056b3;
}
</style>
