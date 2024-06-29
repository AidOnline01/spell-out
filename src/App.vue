<script setup lang="ts">
import Speech from 'speak-tts'
import { ref } from 'vue'

const speech = new Speech()
let voices = []

speech
  .init({
    volume: 0.5,
    lang: 'en-GB',
    rate: 1,
    pitch: 1
  })
  .then((data) => {
    const excludedVoices = [
      'Microsoft Zira - English (United States)',
      'Microsoft David - English (United States)',
      'Microsoft Mark - English (United States)'
    ]
    voices = data.voices.filter(
      (voice) => voice.lang.startsWith('en-') && !excludedVoices.includes(voice.name)
    )
  })

const generateRandomWord = (length: number): string => {
  const alphabet = 'a b c d e f g h i j k l m n o p q r s t u v w x y z'
  const letters = alphabet.split(' ')

  let word = ''
  for (let i = 0; i <= length; i++) {
    const randomLetter = letters[Math.round(Math.random() * 25)]
    word += randomLetter
  }

  return word
}

const randomWord = ref('')
const typedWord = ref('')
const typedWordInput = ref()

const changeVoice = () => {
  const randomVoice = voices[Math.round(Math.random() * (voices.length - 1))]
  speech.setVoice(randomVoice.name)
}

const speak = (word) => {
  speech.speak({
    text: 'I will start now... ' + word.split('').join('. '),
    queue: false
  })
}

const play = () => {
  typedWord.value = ''
  typedWordInput.value.focus()

  speech.pause()

  isChecked.value = false
  randomWord.value = generateRandomWord(10)

  changeVoice()

  speak(randomWord.value)
}

const repeat = () => {
  speech.pause()

  speak(randomWord.value)
}

const isChecked = ref(false)
const isCorrect = ref(false)

const check = () => {
  isCorrect.value = typedWord.value === randomWord.value

  isChecked.value = true
}
</script>

<template>
  <div id="app">
    <div class="wrapper">
      <button @click="play()" class="play">Play</button>
      <div :class="['answer', isCorrect ? 'is-correct' : 'is-incorrect']" v-if="isChecked">
        {{ randomWord }}
      </div>
      <input class="spell-out" type="text" v-model="typedWord" ref="typedWordInput" />
      <button clas="check" @click="check()">Check</button>
      <button class="repeat" @click="repeat()">Repeat</button>
    </div>
  </div>
</template>

<style scoped>
#app {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  font-size: 20px;
}

button,
input {
  font-size: inherit;
}

.answer {
}

.answer.is-correct {
  background: green;
}

.answer.is-incorrect {
  background: red;
}

.play {
  display: block;
  margin-bottom: 10px;
}

.spell-out {
}

.check {
  display: block;
}

.repeat {
  display: block;
  margin-top: 10px;
}
</style>
