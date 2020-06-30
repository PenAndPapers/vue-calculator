<template>
  <div class="calculator">
    <div class="div">
      <input
        type="text"
        autofocus
        v-model="equation"
        @keypress.stop.prevent="restrictNonNumericKeys"
      >
    </div>
    <div class="grid">
      <div class="grid numbers">
        <button @click="getSine">sin</button>
        <button @click="getCosine">cos</button>
        <button @click="getTangent">tan</button>
        <button @click="getLog">log</button>
        <button @click="getPowerOf">x<sup>2</sup></button>
        <button @click="getPercentage">%</button>
        <button @click="addNumber(7)">7</button>
        <button @click="addNumber(8)">8</button>
        <button @click="addNumber(9)">9</button>
        <button @click="addNumber(4)">4</button>
        <button @click="addNumber(5)">5</button>
        <button @click="addNumber(6)">6</button>
        <button @click="addNumber(1)">1</button>
        <button @click="addNumber(2)">2</button>
        <button @click="addNumber(3)">3</button>
        <button @click="addNumber(0)">0</button>
        <button @click="addNumber('.')">&bull;</button>
        <button @click="calculate">=</button>
      </div>
      <div class="grid operations">
        <button @click="getSquareRoot">&radic;</button>
        <button @click="addOperation('/')">/</button>
        <button @click="addOperation('*')">*</button>
        <button @click="addOperation('-')">-</button>
        <button @click="addOperation('+')">+</button>
        <button @click="clearEquation">CE</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'VCalculator',
  data () {
    return {
      answer: '',
      number: '',
      equation: '',
      lastOperation: '',
      operations: '+-*/',
      nums: [],
      powerOf: 0,
      isPowerOf: false
    }
  },
  methods: {
    clearEquation () {
      this.equation = ''
    },
    getTangent () {
      this.equation = this.equation ? Math.tan(this.equation) : ''
    },
    getCosine () {
      this.equation = this.equation ? Math.cos(this.equation) : ''
    },
    getSine () {
      this.equation = this.equation ? Math.sin(this.equation) : ''
    },
    getLog () {
      this.equation = this.equation ? Math.log(this.equation) : ''
    },
    getSquareRoot () {
      this.equation = this.equation ? Math.sqrt(this.equation) : ''
    },
    getPowerOf () {
      /**
       * Issues to fix
       * - powerOf is not yet working
       * -
       */
      this.equation = this.equation ? Math.pow(this.equation) : ''
    },
    getPercentage () {
      this.equation = this.equation ? this.equation / 100 : ''
    },
    addNumber (number) {
      this.equation += number
    },
    addOperation (operation) {
      console.log(operation)
      const lastChar = this.equation.substring(this.equation.length - 1, this.equation.length)
      const isLastCharANumber = Number.isInteger(parseInt(lastChar))

      // restrict adding of operation in a row
      if (isLastCharANumber) {
        this.equation += ' ' + operation + ' '
      }
    },
    calculate () {
      // remove empty items
      const items = this.equation.split(' ').filter(item => item)
      let operation = ''
      this.nums = []

      if (items.length < 3) return

      /**
       * Issues to fix
       * - follow MDAS opreration
       * - restrict alphakeys
       */
      items.forEach(item => {
        if (this.operations.indexOf(item) > -1) {
          operation = item
        }

        if (this.operations.indexOf(item) < 0) {
          this.nums.push(parseFloat(item))
        }

        // addition
        if (operation === '+' && this.nums.length === 2) {
          this.nums = [this.nums[0] + this.nums[1]]
        }

        // subtraction
        if (operation === '-' && this.nums.length === 2) {
          this.nums = [this.nums[0] - this.nums[1]]
        }

        // mutiplication
        if (operation === '*' && this.nums.length === 2) {
          this.nums = [this.nums[0] * this.nums[1]]
        }

        // divition
        if (operation === '/' && this.nums.length === 2) {
          this.nums = [this.nums[0] / this.nums[1]]
        }

        // display whole number if value has no decimal
        // otherwise round of to two decimal number
        this.equation = parseInt(this.nums[0]) === this.nums[0] ? this.nums.toString() : this.nums[0].toFixed(2).toString()
      })
    },
    restrictNonNumericKeys ($event) {
      const regex = /([0-9+\-*/])/g
      // key must be valid
      if (regex.test($event.key)) {
        // add space around operators
        if (this.operations.indexOf($event.key) > -1) return this.addOperation($event.key)
        // no space around numbers
        this.equation += $event.key
      }
      // calculate on press Enter key
      if ($event.key === 'Enter') this.calculate()
    }
  }
}
</script>
