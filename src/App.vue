<script setup lang="ts">
import Speech from 'speak-tts'
import { ref } from 'vue'
import words from 'an-array-of-english-words'

const speech = new Speech()
let voices = ref([])

const changeVoice = (voiceName) => {
  speech.setVoice(voiceName)
}

speech
  .init({
    volume: 0.5,
    lang: 'en-GB',
    rate: 1
  })
  .then((data) => {
    const excludedVoices = [
      'Microsoft Zira - English (United States)',
      'Microsoft David - English (United States)',
      'Microsoft Mark - English (United States)'
    ]
    voices.value = data.voices.filter(
      (voice) => voice.lang.startsWith('en-') && !excludedVoices.includes(voice.name)
    )

    changeVoice(voices.value[0].name)
  })

const generateRandomWord = (): string => {
  const filteredWords = words.filter((word) => true)

  return filteredWords[Math.round(Math.random() * filteredWords.length - 1)]
}

const randomWord = ref('')
const typedWord = ref('')
const typedWordInput = ref()

const speak = (word) => {
  speech.speak({
    text: 'I will start now... ' + word.split('').join('. next . '),
    queue: false
  })
}

const play = () => {
  typedWord.value = ''
  typedWordInput.value.focus()

  speech.pause()

  isChecked.value = false
  randomWord.value = generateRandomWord()

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
    <form class="wrapper" @submit.prevent="check()">
      <select
        class="voices"
        name="voices"
        @change="(e) => changeVoice((e.target as HTMLSelectElement).value)"
      >
        <option v-for="(voice, index) in voices" :value="voice.name" :key="index">
          {{ voice.name }}
        </option>
      </select>

      <button type="button" @click="play()" class="play">Play</button>
      <div :class="['answer', isCorrect ? 'is-correct' : 'is-incorrect']" v-if="isChecked">
        {{ randomWord }}
      </div>

      <input
        class="spell-out"
        :type="isChecked ? 'text' : 'password'"
        v-model="typedWord"
        ref="typedWordInput"
      />
      <button type="button" class="check" @click="check()">Check</button>
      <button type="button" class="repeat" @click="repeat()">Repeat</button>
    </form>
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

.voices {
  margin-bottom: 20px;
  padding: 10px;
}

.answer {
  padding: 10px;
  letter-spacing: 2px;
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
  padding: 10px;
  letter-spacing: 2px;
  font-family: roboto;
}

.check {
  display: block;
  margin-top: 10px;
}

.repeat {
  display: block;
  margin-top: 10px;
}
</style>
