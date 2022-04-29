<template>
  <div class="content">
    <div class="container">
      <div class="Search__container">
        <div class="Search__inputs">
          <input
            v-model="inputFunction"
            class="Search__input"
            placeholder="функция"
            type="search" name="search"
          >
          <input
            v-model.number="epsilon"
            class="Search__input"
            placeholder="погрешность"
            type="search" name="search"
          >
          <input
            v-model.number="startPoint"
            class="Search__input"
            placeholder="начальная точка"
            type="search" name="search"
          >
          <input
            v-model.number="endPoint"
            class="Search__input"
            placeholder="конечная точка"
            type="search" name="search"
          >
          <input
            v-model.number="step"
            class="Search__input"
            placeholder="шаг"
            type="search" name="search"
          >
        </div>
        <div class="Search-functions">
          <button class="Search__button" @keyup.enter="makeContent" @click="makeContent">Find</button>
        </div>
      </div>

      <div class="error-message" v-if="showInputError">
       {{ errorInputMessage }}
      </div>
      <div class="error-message" v-if="showFunctionError">
       {{ errorFunctionMessage }}
      </div>
      <hr>
      <div v-if="loading" class="loading">
        🔧  Building Charts ...
        <div class="sk-cube-grid">
        <div class="sk-cube sk-cube1"></div>
        <div class="sk-cube sk-cube2"></div>
        <div class="sk-cube sk-cube3"></div>
        <div class="sk-cube sk-cube4"></div>
        <div class="sk-cube sk-cube5"></div>
        <div class="sk-cube sk-cube6"></div>
        <div class="sk-cube sk-cube7"></div>
        <div class="sk-cube sk-cube8"></div>
        <div class="sk-cube sk-cube9"></div>
      </div>
      </div>

      <div class="Chart__container" v-if="loaded">
        <div class="Chart__title">
          <h2>My graphic</h2>
        </div>
        <hr>
        <div class="Chart__content">
          <line-chart chart-id="line-daily" v-if="loaded" :chart-data="yValues" :chart-labels="xValues"/>
        </div>
      </div>
      <div class="Results__container" v-if="dihotomyData.length">
        <results-list :overallData="overallData" :dihotomyData="dihotomyData" :hordData="hordData" :newtonData="newtonData"/>
      </div>
    </div>
  </div>
</template>

<script>
  import LineChart from '@/components/LineChart'
  import ResultsList from '@/components/ResultsList'
  import { evaluate } from 'mathjs'

  export default {
    components: { LineChart, ResultsList },

    data () {
      return {
        inputFunction: 'sin(x)',
        epsilon: 0.01,
        startPoint: 0.1,
        endPoint: 10,
        step: 0.1,
        dihotomyData: [],
        hordData: [],
        newtonData: [],
        xValues: [],
        yValues: [],
        intervals: [],
        loaded: false,
        loading: false,
        showInputError: false,
        showFunctionError: false,
        errorInputMessage: 'Заполните данные полностью',
        errorFunctionMessage: 'Введите в поле функцию правильно, пример: tan(x), x^3'
      }
    },

    computed: {
      isValidateFuncInput () {
        return this.inputFunction !== null && this.inputFunction !== '' && this.inputFunction !== 'undefined'
      },

      isValidateEpsInput () {
        return this.epsilon !== 0 && this.epsilon !== null && this.epsilon !== '' && this.epsilon !== 'undefined'
      },

      isValidateStartPointInput () {
        return this.startPoint !== null && this.startPoint !== '' && this.startPoint !== 'undefined'
      },

      isValidateEndPointInput () {
        return this.endPoint !== null && this.endPoint !== '' && this.endPoint !== 'undefined'
      },

      isValidateStepInput () {
        return this.step !== 0 && this.step !== null && this.step !== '' && this.step !== 'undefined'
      },

      isValidateInputs () {
        return this.isValidateFuncInput && this.isValidateEpsInput && this.isValidateStartPointInput && this.isValidateEndPointInput && this.isValidateStepInput
      },

      teoreticSteps () {
        if (this.intervals.length) {
          let a = this.intervals[0][0]
          let b = this.intervals[0][1]
          let teoreticSteps = (Math.log(b - a) - Math.log(this.epsilon)) / Math.log(2) + 1
          return Math.floor(teoreticSteps)
        }
      },

      overallData () {
        if (this.isValidateEpsInput) {
          return [this.epsilon, this.teoreticSteps]
        }
      }
    },

    methods: {
      resetState () {
        this.loaded = false
        this.loading = true
        this.showInputError = false
        this.showFunctionError = false
        this.xValues = []
        this.yValues = []
      },

      makeContent () {
        this.makeGraphic()
        this.makeIntervals()
        this.FindRoots()
      },

      makeGraphic () {
        if (!this.isValidateInputs) {
          this.showInputError = true
          this.loading = false
          return
        }
        this.resetState()

        for (let i = this.startPoint; i <= this.endPoint; i += this.step) {
          let normalIterator = Number(i.toPrecision(2))

          try {
            this.xValues = [...this.xValues, normalIterator]
            this.yValues = [...this.yValues, this.myFunction(normalIterator)]
          } catch (e) {
            this.showFunctionError = true
            this.loaded = false
            this.loading = false
            return
          }
        }

        setTimeout(() => {
          this.loaded = true
          this.loading = false
        }, 0)
      },

      myFunction (arg) {
        return evaluate(this.inputFunction, { x: arg })
      },

      makeIntervals () {
        this.intervals = []

        for (let i = 1; i < this.yValues.length; i++) {
          if (this.yValues[i - 1] * this.yValues[i] < 0) {
            let a = this.xValues[i - 1]
            let b = this.xValues[i]
            this.intervals = [...this.intervals, [a, b]]
          }
        }
      },

      FindRoots () {
        if (this.intervals.length) {
          for (let interval of this.intervals) {
            this.dihotomyMethod(interval[0], interval[1])
            this.hordMethod(interval[0], interval[1])
            this.newtonMethod(interval[0], interval[1])
          }
        }
      },

      dihotomyMethod (a, b) {
        let step = 0
        let x = 0

        while ((b - a) / 2 > this.epsilon) {
          step++
          x = (a + b) / 2

          if ((this.myFunction(a) * this.myFunction(x)) > 0) {
            a = x
          } else {
            b = x
          }
        }
        this.dihotomyData = [...this.dihotomyData, [x, this.myFunction(x), step]]

        console.log('dihotomyData', this.dihotomyData)
      },

      hordMethod (a, b) {
        let step = 0

        while (Math.abs(this.myFunction(b)) > this.epsilon) {
          step++
          b = b - ((b - a) * this.myFunction(b) / (this.myFunction(b) - this.myFunction(a)))
        }
        this.hordData = [...this.hordData, [b, this.myFunction(b), step]]

        console.log('hordData', this.hordData)
      },

      newtonMethod (a, b) {
        let step = 0
        let x = 0

        while ((b - a) / 2 > this.epsilon) {
          step++
          x = (a + b) / 2

          if ((this.myFunction(a) * this.myFunction(x)) > 0) {
            a = x
          } else {
            b = x
          }
        }
        this.newtonData = [...this.newtonData, [x, this.myFunction(x), step]]

        console.log('newtonData', this.newtonData)
      }
    }
  }
</script>

<style lang="scss">
  @import "../assets/styles/_variables.scss";

  hr {
    display: block;
    height: 1px;
    border: 0;
    border-top: 1px solid color(solitude);
    margin: 1em 0;
    padding: 0;
  }

  .content {
    background: color(ghost-white);
    min-height: calc(100vh - 207px);
  }

  .title {
    text-align: center;
    color: color(fjord);
  }

  .container {
    @include container();
  }

  .loading {
    text-align: center;
    color: color(fjord);
    font-size: rem(18);
  }

  .error-message {
    text-align: center;
    color: color(robin-egg-blue);
  }

  .Search {
    @include has(container) {
      max-width: rem(640);
      padding: rem(100) 0 rem(20) 0;
      margin: 0 auto;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;

      @include media($sm-up) {
        flex-direction: row;
      }
    }
    @include has(input) {
      padding: rem(20);
      background-color: #fff;
      border: 1px solid color(solitude);
      font-size: rem(16);
      flex: 1;
    }

    @include has(button) {
      appearance: none;
      padding: rem(18) rem(45);
      margin-left: rem(20);
      border: 0;
      border-radius: 0;
      cursor: pointer;
      text-align: center;
      font-size: rem(21);
      font-weight: 600;
      color: #fff;
      background: color(robin-egg-blue);

      @include hover-active-states {
        background: darken(color(robin-egg-blue), 10%);
      }
    }

    @include has(icon) {
      width: rem(40);
      height: rem(40);
      margin-left: rem(15);
      cursor: pointer;
      color: color(fjord);

      @include hover-active-states {
        color: color(robin-egg-blue);
      }

      > svg {
        fill: currentColor;
      }
    }

    @include has(settings) {
      max-width: rem(640);
      margin: 0 auto;
      padding: rem(20) 0;
      display: flex;
      flex-direction: row;
      justify-content: center;

        .vdp-datepicker + .vdp-datepicker {
          margin-left: 1.25rem;
          flex: 1;
        }
    }
  }

  .Search-functions {
    align-self: self-start;
  }

  .Chart__container {
    border-radius: $base-border-radius;
    background-color: #fff;
    box-shadow: 0 15px 30px 0 rgba(0,0,0,.11), 0 5px 15px 0 rgba(0,0,0,.08);
    padding: rem(20) rem(40);
    margin: rem(50) 0;
  }

  .Chart__title {
    display: flex;
    flex-direction: row;
    margin-bottom: rem(20);
    justify-content: space-between;

    h2 {
      display: flex;
      align-items: center;
      color: color(fjord);
      margin: 0;
      font-weight: 600;
      font-size: rem(16);

      > span {
        font-weight: 400;
        color: color(robin-egg-blue);
        font-size: rem(16);
        margin-left: rem(25);
      }
    }
  }

  .sk-cube-grid {
    width: 40px;
    height: 40px;
    margin: 100px auto;
  }

  .sk-cube-grid .sk-cube {
    width: 33%;
    height: 33%;
    background-color: color(robin-egg-blue);
    float: left;
    -webkit-animation: sk-cubeGridScaleDelay 1.3s infinite ease-in-out;
            animation: sk-cubeGridScaleDelay 1.3s infinite ease-in-out;
  }
  .sk-cube-grid .sk-cube1 {
    -webkit-animation-delay: 0.2s;
            animation-delay: 0.2s; }
  .sk-cube-grid .sk-cube2 {
    -webkit-animation-delay: 0.3s;
            animation-delay: 0.3s; }
  .sk-cube-grid .sk-cube3 {
    -webkit-animation-delay: 0.4s;
            animation-delay: 0.4s; }
  .sk-cube-grid .sk-cube4 {
    -webkit-animation-delay: 0.1s;
            animation-delay: 0.1s; }
  .sk-cube-grid .sk-cube5 {
    -webkit-animation-delay: 0.2s;
            animation-delay: 0.2s; }
  .sk-cube-grid .sk-cube6 {
    -webkit-animation-delay: 0.3s;
            animation-delay: 0.3s; }
  .sk-cube-grid .sk-cube7 {
    -webkit-animation-delay: 0s;
            animation-delay: 0s; }
  .sk-cube-grid .sk-cube8 {
    -webkit-animation-delay: 0.1s;
            animation-delay: 0.1s; }
  .sk-cube-grid .sk-cube9 {
    -webkit-animation-delay: 0.2s;
            animation-delay: 0.2s; }

  @-webkit-keyframes sk-cubeGridScaleDelay {
    0%, 70%, 100% {
      -webkit-transform: scale3D(1, 1, 1);
              transform: scale3D(1, 1, 1);
    } 35% {
      -webkit-transform: scale3D(0, 0, 1);
              transform: scale3D(0, 0, 1);
    }
  }

  @keyframes sk-cubeGridScaleDelay {
    0%, 70%, 100% {
      -webkit-transform: scale3D(1, 1, 1);
              transform: scale3D(1, 1, 1);
    } 35% {
      -webkit-transform: scale3D(0, 0, 1);
              transform: scale3D(0, 0, 1);
    }
  }
</style>
