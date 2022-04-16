<template>
  <div id="app">
    <div v-show="training" class="training">Training...</div>
    <div class="canva">
      <div class="display">
        <div v-for="(cell, i) in display" :key="i" @click="setCell(i)" class="cell" :class="{black: cell}">
        </div>
      </div>
      <div class="result">
        <div v-for="(r, i) in result" :key="i">
          {{i}}: {{r.toFixed(2)}}%
        </div>
      </div>
    </div>

    <button @click="activate">activate</button>
    <button @click="train">train</button>
    <p><b>Activate:</b> Запускає нейронку</p>
    <p><b>Train:</b> запускає навчання по забитими мною прикладами (комп підвисне, треба почекати поки зникне лоадер)</p>
    <p><b>0-9:</b> корегування результату, натискати тільки після того як увів щось на циферблаті і нажав на <b>activate</b></p>
    <hr>
    <h3>Шаблони</h3>
    <p>По цим шаблонам нейронка вчиться</p>
    <div class="templates">
      <div v-for="(template, i) in trainingNumbers" :key="i"  class="canva">
        <b>{{i}}:</b>
        <div class="display">
          <div v-for="(cell, j) in template" :key="j" @click="changeTemplate(cell, i, j)" class="cell" :class="{black: cell}"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const synaptic = require('synaptic');
const Network = synaptic.Network;
const Layer = synaptic.Layer;

export default {
  name: 'App',
  data() {
    return {
      display: new Array(15).fill(0),
      net: null,
      result: new Array(10).fill(0),
      lr: 0.3,
      training: false,
      numbersData: [
        [1, 0, 0, 0, 0, 0, 0, 0, 0, 0],
        [0, 1, 0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 1, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 1, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 1, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 1, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 1, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 1, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0, 1, 0],
        [0, 0, 0, 0, 0, 0, 0, 0, 0, 1],
      ],
      trainingNumbers: [
        [1,1,1,1,0,1,1,0,1,1,0,1,1,1,1],
        [0,1,0,0,1,0,0,1,0,0,1,0,0,1,0],
        [1,1,1,0,0,1,1,1,1,1,0,0,1,1,1],
        [1,1,1,0,0,1,1,1,1,0,0,1,1,1,1],
        [1,0,1,1,0,1,1,1,1,0,0,1,0,0,1],
        [1,1,1,1,0,0,1,1,1,0,0,1,1,1,1],
        [1,1,1,1,0,0,1,1,1,1,0,1,1,1,1],
        [1,1,1,0,0,1,0,0,1,0,0,1,0,0,1],
        [1,1,1,1,0,1,1,1,1,1,0,1,1,1,1],
        [1,1,1,1,0,1,1,1,1,0,0,1,1,1,1]
      ]
    }
  },
  methods: {
    setCell(i) {
      let newVal = this.display[i] ? 0 : 1;
      this.$set(this.display, i, newVal);
    },
    activate() {
      let result = this.net.activate(this.display);
      this.result = result;
    },
    train() {
      this.training = true;
      setTimeout(() => {
        for (var i = 0; i < 10000; i++)
        {
          // 0
          this.net.activate(this.trainingNumbers[0]);
          this.net.propagate(this.lr, this.numbersData[0]);

          // 1
          this.net.activate(this.trainingNumbers[1]);
          this.net.propagate(this.lr, this.numbersData[1]);

          // 2
          this.net.activate(this.trainingNumbers[2]);
          this.net.propagate(this.lr, this.numbersData[2]);

          // 3
          this.net.activate(this.trainingNumbers[3]);
          this.net.propagate(this.lr, this.numbersData[3]);

          // 4
          this.net.activate(this.trainingNumbers[4]);
          this.net.propagate(this.lr, this.numbersData[4]);

          // 5
          this.net.activate(this.trainingNumbers[5]);
          this.net.propagate(this.lr, this.numbersData[5]);

          // 6
          this.net.activate(this.trainingNumbers[6]);
          this.net.propagate(this.lr, this.numbersData[6]);

          // 7
          this.net.activate(this.trainingNumbers[7]);
          this.net.propagate(this.lr, this.numbersData[7]);

          // 8
          this.net.activate(this.trainingNumbers[8]);
          this.net.propagate(this.lr, this.numbersData[8]);

          // 9
          this.net.activate(this.trainingNumbers[9]);
          this.net.propagate(this.lr, this.numbersData[9]);

        }
        setTimeout(() => {
          this.training = false;
        }, 10);
      }, 10);
    },
    propagate(i) {
      this.net.propagate(this.lr, this.numbersData[i]);
    },
    changeTemplate(cell, i, j) {
      let newVal = cell ? 0 : 1;
      let newRow = this.trainingNumbers[i].slice(0);
      newRow[j] = newVal;
      this.$set(this.trainingNumbers, i, newRow);
    }
  },
  created() {
    let inputLayer = new Layer(15);
    let hiddenLayer = new Layer(30);
    let outputLayer = new Layer(10);

    inputLayer.project(hiddenLayer);
    hiddenLayer.project(outputLayer);

    this.net = new Network({
      input: inputLayer,
      hidden: [hiddenLayer],
      output: outputLayer
    });
  }
}
</script>

<style lang="scss">
#app {
  .templates {
    display: flex;
    flex-wrap: wrap;
    font-size: 18px;
    .display {
      margin-left: 14px;
    }
  }
  .training {
    position: fixed;
    width: 100%;
    height: 100%;
    background-color: rgba(255,255,255,0.9);
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 64px;
  }
  .canva {
    display: flex;
    .result {
      display: flex;
      flex-direction: column;
      div {
        margin-top: 16px;
        font-size: 18px;
      }
    }
  }
  button {
    height: 40px;
    cursor: pointer;
    margin: 10px;
    min-width: 40px;
  }
  .display {
    display: flex;
    flex-wrap: wrap;
    width: 240px;
    height: 400px;
    border: 1px solid #333;
    user-select: none;
    margin-bottom: 20px;margin-right: 40px;
    .cell {
      width: 80px;
      height: 80px;
      border: 1px solid rgba(0,0,0,0.4);
      box-sizing: border-box;
      cursor: pointer;
      transition: 0.3s ease-in-out;
      display: flex;
      justify-content: center;
      align-items: center;
      &:hover {
        background-color: rgba(0,0,0,0.2);
      }
      &.black {
        background-color: black;
      }
    }
  }
}
</style>
