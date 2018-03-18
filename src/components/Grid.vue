<template>
<center>
  <strong>wave : {{ generation }}</strong>
  <div class="board">
    <div class="row" v-for="(row, i) in cells" :key="i">
      <span
        class="cell"
        :style="`background: ${ cells[i][j] ? '#c576db' : '#fff' }`"
        v-for="(cell, j) in row"
        :key="j"
        @click="toggle(i, j)"
      >
        &nbsp;
      </span>
    </div>
  </div>
  <div class="controls">
    <button @click="next">NEXT</button>
    <button v-if="interval" @click="stop">STOP</button>
    <button v-else @click="auto">PLAY</button>
    &nbsp;|&nbsp;
    <button @click="reset">Reset wave</button>
    &nbsp;|&nbsp;
    <button @click="clear">Clear</button>
    <button @click="random">Random</button>
    <br><br>
    <button @click="block">Block</button>
    <button @click="beehive">Beehive</button>
    <button @click="loaf">Loaf</button>
    <button @click="boat">Boat</button>
    <button @click="tub">Tub</button>
    &nbsp;|&nbsp;
    <button @click="blinker">Blinker</button>
    <button @click="toad">Toad</button>
    <button @click="beacon">Beacon</button>
    <button @click="pulsar">Pulsar</button>
    <button @click="pentadecathlon">Pentadecathlon</button>
    &nbsp;|&nbsp;
    <button @click="glider">Glider</button>
    <button @click="spaceship">Spaceship</button>
    &nbsp;|&nbsp;
    <button @click="rPentomino">R-Pentomino</button>
    <button @click="diehard">Diehard</button>
    <button @click="acorn">Acorn</button>
    &nbsp;|&nbsp;
    <button @click="next">Gosper Glider gun</button>
    <button @click="lineBeacon">Line to beacon</button>
  </div>
</center>
</template>

<script>
const width = 80
const height = 50
const empty = Array.from(Array(height), x => Array.from(Array(width), y => false))

export default {
  name: 'Grid',

  data: () => ({
    cells: empty.map(row => row.map(cell => cell)),
    interval: null,
    speed: 1,
    generation: 0
  }),

  methods: {
    toggle (i, j) {
      this.cells[i][j] = !this.cells[i][j]
      this.$forceUpdate()
    },

    next () {
      this.cells = this.cells.map((row, i) =>
        row.map((val, j) => {
          let count = 0
          // to check :
          // -1 -1 | -1 0 | -1 1 | 0 -1 | 0 1 | 1 -1 | 1 0 | 1 1
          for (let a = -1; a <= 1; a++) {
            for (let b = -1; b <= 1; b++) {
              if (a === 0 && b === 0) {
                continue
              }

              if (this.cells[i + a] && this.cells[i + a][j + b]) {
                count++
              }
            }
          }
          if (!val && count === 3) {
            return true
          }
          if (val && count !== 2 && count !== 3) {
            return false
          }
          return val
        }))
      this.generation += 1
    },

    clear () {
      this.cells = this.cells.map(row => row.map(cell => false))
      this.$forceUpdate()
    },

    softClear () {
      this.cells = this.cells.map(row => row.map(cell => false))
      this.generation = 0
    },

    random () {
      for (let i = 0; i < width; i++) {
        for (let j = 0; j < height; j++) {
          this.cells[j][i] = Math.random() < 0.5
        }
      }
      this.$forceUpdate()
    },

    auto () {
      clearInterval(this.interval)
      const interval = setInterval(this.next, this.speed)
      this.interval = interval
    },

    stop () {
      clearInterval(this.interval)
      this.interval = null
    },

    reset () {
      this.stop()
      this.generation = 0
    },

    block () {
      this.softClear()
      this.cells[18][37] = true
      this.cells[18][38] = true
      this.cells[19][37] = true
      this.cells[19][38] = true
    },

    beehive () {
      this.softClear()
      this.cells[23][30] = true
      this.cells[22][31] = true
      this.cells[22][32] = true
      this.cells[23][33] = true
      this.cells[24][32] = true
      this.cells[24][31] = true
    },

    loaf () {
      this.softClear()
      this.cells[22][30] = true
      this.cells[23][31] = true
      this.cells[24][32] = true
      this.cells[23][33] = true
      this.cells[22][33] = true
      this.cells[21][32] = true
      this.cells[21][31] = true
    },

    boat () {
      this.softClear()
      this.cells[18][35] = true
      this.cells[18][34] = true
      this.cells[19][34] = true
      this.cells[20][35] = true
      this.cells[19][36] = true
    },

    tub () {
      this.softClear()
      this.cells[18][37] = true
      this.cells[19][36] = true
      this.cells[19][38] = true
      this.cells[20][37] = true
    },

    blinker () {
      this.softClear()
      this.cells[18][33] = true
      this.cells[18][34] = true
      this.cells[18][35] = true
    },

    toad () {
      this.softClear()
      this.cells[18][33] = true
      this.cells[18][34] = true
      this.cells[18][35] = true
      this.cells[17][32] = true
      this.cells[17][33] = true
      this.cells[17][34] = true
    },

    beacon () {
      this.softClear()
      this.cells[18][37] = true
      this.cells[18][38] = true
      this.cells[19][37] = true
      this.cells[19][38] = true
      this.cells[20][39] = true
      this.cells[20][40] = true
      this.cells[21][39] = true
      this.cells[21][40] = true
    },

    pulsar () {
      this.softClear()
      this.cells[19][26] = true
      this.cells[19][27] = true
      this.cells[19][28] = true
      this.cells[18][29] = true
      this.cells[17][29] = true
      this.cells[16][29] = true
      this.cells[16][31] = true
      this.cells[17][31] = true
      this.cells[18][31] = true
      this.cells[19][32] = true
      this.cells[19][33] = true
      this.cells[19][34] = true
      this.cells[21][34] = true
      this.cells[21][33] = true
      this.cells[21][32] = true
      this.cells[22][31] = true
      this.cells[23][31] = true
      this.cells[24][31] = true
      this.cells[24][29] = true
      this.cells[23][29] = true
      this.cells[22][29] = true
      this.cells[21][28] = true
      this.cells[21][27] = true
      this.cells[21][26] = true
      this.cells[18][24] = true
      this.cells[17][24] = true
      this.cells[16][24] = true
      this.cells[14][26] = true
      this.cells[14][27] = true
      this.cells[14][28] = true
      this.cells[14][32] = true
      this.cells[14][34] = true
      this.cells[14][33] = true
      this.cells[26][28] = true
      this.cells[26][27] = true
      this.cells[26][26] = true
      this.cells[26][32] = true
      this.cells[26][33] = true
      this.cells[26][34] = true
      this.cells[22][24] = true
      this.cells[23][24] = true
      this.cells[24][24] = true
      this.cells[22][36] = true
      this.cells[23][36] = true
      this.cells[24][36] = true
      this.cells[18][36] = true
      this.cells[17][36] = true
      this.cells[16][36] = true
    },

    pentadecathlon () {
      this.softClear()
      this.cells[13][35] = true
      this.cells[14][35] = true
      this.cells[16][35] = true
      this.cells[17][35] = true
      this.cells[18][35] = true
      this.cells[19][35] = true
      this.cells[21][35] = true
      this.cells[22][35] = true
      this.cells[20][34] = true
      this.cells[20][36] = true
      this.cells[15][36] = true
      this.cells[15][34] = true
    },

    glider () {
      this.softClear()
      this.cells[0][0] = true
      this.cells[1][1] = true
      this.cells[1][2] = true
      this.cells[2][1] = true
      this.cells[2][0] = true
    },

    spaceship () {
      this.softClear()
      this.cells[13][0] = true
      this.cells[15][0] = true
      this.cells[16][1] = true
      this.cells[16][2] = true
      this.cells[16][3] = true
      this.cells[16][4] = true
      this.cells[15][4] = true
      this.cells[14][4] = true
      this.cells[13][3] = true
    },

    rPentomino () {
      this.softClear()
      this.cells[20][32] = true
      this.cells[20][33] = true
      this.cells[19][33] = true
      this.cells[19][34] = true
      this.cells[21][33] = true
    },

    diehard () {
      this.softClear()
      this.cells[20][28] = true
      this.cells[20][29] = true
      this.cells[21][29] = true
      this.cells[21][33] = true
      this.cells[21][34] = true
      this.cells[21][35] = true
      this.cells[19][34] = true
    },

    acorn () {
      this.softClear()
      this.cells[24][30] = true
      this.cells[24][31] = true
      this.cells[22][31] = true
      this.cells[23][33] = true
      this.cells[24][34] = true
      this.cells[24][36] = true
      this.cells[24][35] = true
    },

    lineBeacon () {
      this.softClear()
      this.cells[18][32] = true
      this.cells[18][33] = true
      this.cells[18][34] = true
      this.cells[18][35] = true
      this.cells[18][36] = true
    }
  }
}
</script>

<style scoped>
  .board {
    padding: 0;
    margin: auto;
    background: #fff;
  }
  .row {
    display: flex;
  }
  .cell {
    border: .2px solid #c576db1a;
    padding: 0;
    margin: 0;
    /* height: 5px; */
    width: 20px;
    cursor: pointer;
  }
  .cell.empty {
    background: #fff;
  }
  .cell.filled {
    background: #000;
  }
  strong {
    color: #8b81fc;
  }
  .controls {
    margin-top: 20px;
  }
  button {
    background: #8b81fc;
    /* background: linear-gradient(20deg, #c576db, #8b81fc); */
    border: none;
    padding: 10px;
    border-radius: 5px;
    cursor: pointer;
    color: #fff;
    font-weight: bold;
    font-family: 'Raleway';
    font-size: 15px;
    border: 2px solid transparent;
    transition: all .6s;
    box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2), 0 3px 10px 0 rgba(0,0,0,0.19);
  }
  button:hover {
    background: #fff;
    border: 2px solid #8b81fc;
    color: #8b81fc;
  }
</style>
