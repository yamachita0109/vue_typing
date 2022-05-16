<template>
  <div>
    <div class="mb-10">
      <strong class="text-3xl font-thin leading-none text-neutral-600 lg:text-4xl">
        {{ interval.toFixed(2) }}
      </strong>
    </div>
    <div class="mb-10" v-show="active">
      <strong class="text-3xl font-thin leading-none text-neutral-600 lg:text-4xl">
        {{ question }}
      </strong>
    </div>
    <div v-show="active" class="w-full text-center mb-10">
      <div  class="items-center border-b border-teal-500 py-2">
        <input
          v-model="val"
          :disabled="finish"
          type="text"
          class="appearance-none bg-transparent border-none w-full text-gray-700 mr-3 py-1 px-2 leading-tight focus:outline-none"
        />
      </div>
    </div>
    <div v-show="!active" class="mb-10">
      <button
        v-on:click="clickStart"
        class="bg-blue-500 hover:bg-blue-400 text-white font-bold py-2 px-4 border-b-4 border-blue-700 hover:border-blue-500 rounded"
      >Start.</button>
    </div>
    <div v-show="finish" class="mb-10">
      <button
        v-on:click="clickRetry"
        class="bg-blue-500 hover:bg-blue-400 text-white font-bold py-2 px-4 border-b-4 border-blue-700 hover:border-blue-500 rounded"
      >Retry.</button>
    </div>

  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'stopWatch',
  data(){
    return {
      active: false,
      finish: false,
      start: 0,
      timer: 0,
      interval: 0,
      accum: 0,
      question: '',
      val: '',
    }
  },
  watch: {
    val: function(v) {
      if (v === this.question) {
        this.stopTimer()
        this.finish = true;
      }
    },
  },
  methods:{
    async clickStart() {
      await this.showQuestion()
      this.active = true;
      this.startTimer()
      this.$refs.input.focus()
    },

    async clickRetry() {
      await this.showQuestion()
      this.finish = false
      this.resetTimer()
      this.startTimer()
      this.val = ''
    },

    async showQuestion() {
      this.question = await this.execApi()
    },

    startTimer() {
      this.start = Date.now()
      this.timer = setInterval( () => {this.interval = this.accum + (Date.now() - this.start) * 0.001}, 10)
    },

    stopTimer() {
      this.accum = this.interval
      clearInterval(this.timer)
    },

    resetTimer() {
      this.interval = 0
      this.accum = 0
      this.start = Date.now()
    },

    async execApi() {
      const res = await axios.create({baseURL: 'https://api.adviceslip.com/advice'}).get()
      return res.data.slip.advice
    }
  }
}
</script>