<template>
  <div class="calculator">
    <input
      type="text"
      autofocus
      v-model="equation"
      @keypress.stop.prevent="restrictKeys"
    >
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
        <button @click="solveEquation">=</button>
      </div>
      <div class="grid operators">
        <button @click="getSquareRoot">&radic;</button>
        <button @click="addOperator('/')">/</button>
        <button @click="addOperator('*')">*</button>
        <button @click="addOperator('-')">-</button>
        <button @click="addOperator('+')">+</button>
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
      lastoperator: '',
      operators: '+-*/',
      nums: [],
      powerOf: 0,
      isPowerOf: false
    }
  },
  methods: {
    addNumber (number) {
      this.equation += number
    },
    addOperator (operator) {
      const lastChar = this.equation.substring(this.equation.length - 1, this.equation.length)
      const isLastCharANumber = Number.isInteger(parseInt(lastChar))

      // concatinate space around operator
      // restrict adding of operator in a row
      // e.g. 2 + - * /
      if (isLastCharANumber) this.equation += ' ' + operator + ' '
    },
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
    getPercentage () {
      this.equation = this.equation ? this.equation / 100 : ''
    },
    getPowerOf () {
      /**
       * Issues to fix
       * - powerOf is not yet working
       */
      this.equation = this.equation ? Math.pow(this.equation) : ''
    },
    getSquareRoot () {
      this.equation = this.equation ? Math.sqrt(this.equation) : ''
    },
    restrictKeys ($event) {
      const regex = /([0-9+\-*/])/g
      // key must be valid
      if (regex.test($event.key)) {
        // concatinate space around operator
        if (this.operators.indexOf($event.key) > -1) return this.addOperator($event.key)
        // no space around numbers
        this.equation += $event.key
      }
      // solveEquation on press Enter key
      if ($event.key === 'Enter') this.solveEquation()
    },
    solveEquation () {
      // remove empty items
      const items = this.equation.split(' ').filter(item => item)
      let operator = ''
      this.nums = []

      // validate atleast two numbers and one operator is present
      if (items.length < 3) return

      /**
       * Issues to fix
       * - follow MDAS opreration
       */
      items.forEach(item => {
        // set operator to use
        if (this.operators.indexOf(item) > -1) operator = item

        // push new number in array
        if (this.operators.indexOf(item) < 0) this.nums.push(parseFloat(item))

        // addition
        if (operator === '+' && this.nums.length === 2) this.nums = [this.nums[0] + this.nums[1]]

        // subtraction
        if (operator === '-' && this.nums.length === 2) this.nums = [this.nums[0] - this.nums[1]]

        // multiplication
        if (operator === '*' && this.nums.length === 2) this.nums = [this.nums[0] * this.nums[1]]

        // division
        if (operator === '/' && this.nums.length === 2) this.nums = [this.nums[0] / this.nums[1]]

        // display whole number if value has no decimal
        // otherwise round to two decimal number
        this.equation = parseInt(this.nums[0]) === this.nums[0] ? this.nums.toString() : this.nums[0].toFixed(2).toString()
      })
    }
  }
}
</script>
