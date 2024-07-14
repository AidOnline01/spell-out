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
    rate: 1,
    pitch: 1
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

const generateRandomWord = (): string => words[Math.round(Math.random() * words.length - 1)]

const randomWord = ref('')
const typedWord = ref('')
const typedWordInput = ref()

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
    <div class="wrapper">
      <select
        class="voices"
        name="voices"
        @change="(e) => changeVoice((e.target as HTMLSelectElement).value)"
      >
        <option v-for="(voice, index) in voices" :value="voice.name" :key="index">
          {{ voice.name }}
        </option>
      </select>

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

.voices {
  margin-bottom: 20px;
  padding: 10px;
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
