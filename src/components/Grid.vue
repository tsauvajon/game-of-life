<template>
<center>
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
    <strong>wave : {{ generation }}</strong>&nbsp;
    <button @click="next"><img src="../assets/skip-forward.svg" height="20" />&nbsp;<span>NEXT WAVE</span></button>
    <button v-if="interval" @click="stop"><img src="../assets/pause.svg" height="20" />&nbsp;<span>STOP</span></button>
    <button v-else @click="auto"><img src="../assets/play.svg" height="20" />&nbsp;<span>PLAY</span></button>
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
    <button @click="phoenix">Phoenix</button>
    <button @click="pulsar">Pulsar</button>
    <button @click="pentadecathlon">Pentadecathlon</button>
    &nbsp;|&nbsp;
    <button @click="glider">Glider</button>
    <button @click="spaceship">Spaceship</button>
    &nbsp;|&nbsp;
    <button @click="rPentomino">R-Pentomino</button>
    <button @click="diehard">Diehard</button>
    <button @click="acorn">Acorn</button>
    <button @click="butterfly">Butterfly</button>
    &nbsp;|&nbsp;
    <button @click="gliderGun">Gosper Glider gun</button>
    <button @click="lineBeacon">Line to beacon</button>
    <button @click="switchEngine">Switch engine</button>
    <button @click="msgInABottle">Ship in a bottle</button>
    <button @click="schickEngine">Schick engine</button>
    <button @click="blinkerShip">Blinker ship</button>
    <br><br>
  </div>
  <div class="saves">
    <button @click="load">Load</button>
    <textarea placeholder="Insert .lif 1.06 content and load" v-model="lifData" />
    <button @click="dump">Dump</button>
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
    generation: 0,
    lifData: ''
  }),

  methods: {
    toggle (i, j) {
      // console.log(`this.cells[${i}][${j}] = ${!this.cells[i][j]}`)
      this.cells[i][j] = !this.cells[i][j]
      this.$forceUpdate()
    },

    load () {
      if (!this.lifData) {
        return
      }

      this.stop()
      this.softClear()

      this.lifData.split('\n').forEach(line => {
        if (line[0] === '#') {
          return
        }

        const coords = line.split(' ')
        if (coords.length !== 2) {
          return
        }

        const x = parseInt(coords[0], 10) + width / 2
        const y = parseInt(coords[1], 10) + height / 2

        this.cells[y][x] = true
      })
      this.$forceUpdate()
    },

    dump () {
      const lifData = this.cells.reduce((acc, row, i) =>
        `${acc}${row.reduce((acc2, cell, j) => cell ? `${acc2}${j - (width / 2)} ${i - (height / 2)}\n` : acc2, '')}`,
      '#Life 1.06\n')
      this.lifData = lifData
    },

    next () {
      const countNeighbours = (i, j) => {
        let count = 0
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
        return count
      }
      const newCells = Array.from(Array(height), x => Array.from(Array(width), y => false))
      const done = {}
      this.cells.forEach((row, i) => {
        row.forEach((val, j) => {
          if (!val) {
            return
          }
          let count = 0
          for (let a = -1; a <= 1; a++) {
            for (let b = -1; b <= 1; b++) {
              if (a === 0 && b === 0) {
                continue
              }

              if (!this.cells[i + a]) {
                continue
              }

              if (this.cells[i + a][j + b]) {
                count++
                continue
              } else if (done[`${i + a},${j + b}`]) {
                continue
              }
              const cellCount = countNeighbours(i + a, j + b)
              if (cellCount === 3) {
                newCells[i + a][j + b] = true
              }
              done[`${i + a},${j + b}`] = true
            }
          }
          if (count === 2 || count === 3) {
            newCells[i][j] = true
          }
        })
      })
      this.cells = newCells
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

    phoenix () {
      this.softClear()
      this.cells[16][29] = true
      this.cells[16][30] = true
      this.cells[18][30] = true
      this.cells[19][32] = true
      this.cells[20][32] = true
      this.cells[19][34] = true
      this.cells[17][35] = true
      this.cells[17][36] = true
      this.cells[15][35] = true
      this.cells[14][33] = true
      this.cells[13][33] = true
      this.cells[14][31] = true
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

    butterfly () {
      this.softClear()
      this.cells[22][29] = true
      this.cells[23][29] = true
      this.cells[24][29] = true
      this.cells[23][30] = true
      this.cells[24][31] = true
      this.cells[25][30] = true
      this.cells[25][31] = true
      this.cells[25][32] = true
    },

    gliderGun () {
      this.softClear()
      this.cells[18][16] = true
      this.cells[18][17] = true
      this.cells[19][17] = true
      this.cells[19][16] = true
      this.cells[19][26] = true
      this.cells[18][26] = true
      this.cells[20][26] = true
      this.cells[21][27] = true
      this.cells[17][27] = true
      this.cells[16][28] = true
      this.cells[16][29] = true
      this.cells[22][28] = true
      this.cells[22][29] = true
      this.cells[21][31] = true
      this.cells[20][32] = true
      this.cells[19][32] = true
      this.cells[18][32] = true
      this.cells[17][31] = true
      this.cells[19][33] = true
      this.cells[19][30] = true
      this.cells[18][36] = true
      this.cells[17][36] = true
      this.cells[16][36] = true
      this.cells[16][37] = true
      this.cells[17][37] = true
      this.cells[18][37] = true
      this.cells[19][38] = true
      this.cells[15][38] = true
      this.cells[14][40] = true
      this.cells[15][40] = true
      this.cells[19][40] = true
      this.cells[20][40] = true
      this.cells[16][50] = true
      this.cells[16][51] = true
      this.cells[17][51] = true
      this.cells[17][50] = true
    },

    lineBeacon () {
      this.softClear()
      this.cells[18][32] = true
      this.cells[18][33] = true
      this.cells[18][34] = true
      this.cells[18][35] = true
      this.cells[18][36] = true
    },

    switchEngine () {
      this.softClear()
      this.cells[25][38] = true
      this.cells[25][37] = true
      this.cells[25][36] = true
      this.cells[24][37] = true
      this.cells[22][36] = true
      this.cells[22][34] = true
      this.cells[23][33] = true
      this.cells[24][34] = true
    },

    msgInABottle () {
      this.softClear()
      this.cells[26][33] = true
      this.cells[27][33] = true
      this.cells[27][34] = true
      this.cells[26][35] = true
      this.cells[25][35] = true
      this.cells[25][34] = true
      this.cells[22][33] = true
      this.cells[21][33] = true
      this.cells[21][32] = true
      this.cells[21][31] = true
      this.cells[20][31] = true
      this.cells[19][32] = true
      this.cells[18][31] = true
      this.cells[18][30] = true
      this.cells[19][29] = true
      this.cells[20][29] = true
      this.cells[21][28] = true
      this.cells[21][27] = true
      this.cells[22][26] = true
      this.cells[23][26] = true
      this.cells[24][27] = true
      this.cells[23][28] = true
      this.cells[23][29] = true
      this.cells[24][29] = true
      this.cells[25][29] = true
      this.cells[25][30] = true
      this.cells[28][30] = true
      this.cells[28][29] = true
      this.cells[29][29] = true
      this.cells[30][29] = true
      this.cells[30][28] = true
      this.cells[29][27] = true
      this.cells[30][26] = true
      this.cells[31][26] = true
      this.cells[32][27] = true
      this.cells[32][28] = true
      this.cells[33][29] = true
      this.cells[34][29] = true
      this.cells[35][30] = true
      this.cells[35][31] = true
      this.cells[34][32] = true
      this.cells[33][31] = true
      this.cells[32][31] = true
      this.cells[32][32] = true
      this.cells[32][33] = true
      this.cells[31][33] = true
      this.cells[31][36] = true
      this.cells[32][36] = true
      this.cells[32][37] = true
      this.cells[32][38] = true
      this.cells[33][38] = true
      this.cells[34][37] = true
      this.cells[35][38] = true
      this.cells[35][39] = true
      this.cells[34][40] = true
      this.cells[33][40] = true
      this.cells[32][41] = true
      this.cells[32][42] = true
      this.cells[31][43] = true
      this.cells[30][43] = true
      this.cells[29][42] = true
      this.cells[30][41] = true
      this.cells[30][40] = true
      this.cells[29][40] = true
      this.cells[28][40] = true
      this.cells[28][39] = true
      this.cells[25][39] = true
      this.cells[25][40] = true
      this.cells[24][40] = true
      this.cells[23][40] = true
      this.cells[23][41] = true
      this.cells[24][42] = true
      this.cells[23][43] = true
      this.cells[22][43] = true
      this.cells[21][42] = true
      this.cells[21][41] = true
      this.cells[20][40] = true
      this.cells[19][40] = true
      this.cells[18][39] = true
      this.cells[18][38] = true
      this.cells[19][37] = true
      this.cells[20][38] = true
      this.cells[21][38] = true
      this.cells[21][37] = true
      this.cells[21][36] = true
      this.cells[22][36] = true
    },

    schickEngine () {
      this.softClear()
      this.cells[14][1 + 58] = true
      this.cells[15][1 + 58] = true
      this.cells[16][1 + 58] = true
      this.cells[16][2 + 58] = true
      this.cells[16][3 + 58] = true
      this.cells[16][4 + 58] = true
      this.cells[15][5 + 58] = true
      this.cells[13][5 + 58] = true
      this.cells[13][2 + 58] = true
      this.cells[17][7 + 58] = true
      this.cells[17][8 + 58] = true
      this.cells[17][9 + 58] = true
      this.cells[18][7 + 58] = true
      this.cells[18][8 + 58] = true
      this.cells[19][7 + 58] = true
      this.cells[19][8 + 58] = true
      this.cells[19][9 + 58] = true
      this.cells[18][10 + 58] = true
      this.cells[18][11 + 58] = true
      this.cells[20][4 + 58] = true
      this.cells[20][3 + 58] = true
      this.cells[20][2 + 58] = true
      this.cells[20][1 + 58] = true
      this.cells[21][1 + 58] = true
      this.cells[22][1 + 58] = true
      this.cells[23][2 + 58] = true
      this.cells[23][5 + 58] = true
      this.cells[21][5 + 58] = true
      this.cells[17][15 + 58] = true
      this.cells[16][14 + 58] = true
      this.cells[16][15 + 58] = true
      this.cells[17][16 + 58] = true
      this.cells[19][16 + 58] = true
      this.cells[19][15 + 58] = true
      this.cells[20][15 + 58] = true
      this.cells[20][14 + 58] = true
      this.cells[18][17 + 58] = true
      this.cells[18][19 + 58] = true
      this.cells[18][20 + 58] = true
      this.cells[18][21 + 58] = true
    },

    blinkerShip () {
      this.softClear()
      this.cells[-7 + 20][-3 + 62] = true
      this.cells[-7 + 20][-2 + 62] = true
      this.cells[-7 + 20][-1 + 62] = true
      this.cells[-7 + 20][0 + 62] = true
      this.cells[-6 + 20][-3 + 62] = true
      this.cells[-6 + 20][1 + 62] = true
      this.cells[-5 + 20][-3 + 62] = true
      this.cells[-4 + 20][-12 + 62] = true
      this.cells[-4 + 20][-11 + 62] = true
      this.cells[-4 + 20][-2 + 62] = true
      this.cells[-4 + 20][1 + 62] = true
      this.cells[-3 + 20][-13 + 62] = true
      this.cells[-3 + 20][-12 + 62] = true
      this.cells[-3 + 20][-10 + 62] = true
      this.cells[-3 + 20][-9 + 62] = true
      this.cells[-2 + 20][-12 + 62] = true
      this.cells[-2 + 20][-11 + 62] = true
      this.cells[-2 + 20][-10 + 62] = true
      this.cells[-2 + 20][-9 + 62] = true
      this.cells[-2 + 20][-5 + 62] = true
      this.cells[-1 + 20][-11 + 62] = true
      this.cells[-1 + 20][-10 + 62] = true
      this.cells[-1 + 20][-6 + 62] = true
      this.cells[-1 + 20][-4 + 62] = true
      this.cells[-1 + 20][-3 + 62] = true
      this.cells[-1 + 20][6 + 62] = true
      this.cells[-1 + 20][11 + 62] = true
      this.cells[-1 + 20][12 + 62] = true
      this.cells[-1 + 20][13 + 62] = true
      this.cells[0 + 20][-7 + 62] = true
      this.cells[0 + 20][-3 + 62] = true
      this.cells[0 + 20][6 + 62] = true
      this.cells[0 + 20][11 + 62] = true
      this.cells[0 + 20][13 + 62] = true
      this.cells[1 + 20][-11 + 62] = true
      this.cells[1 + 20][-10 + 62] = true
      this.cells[1 + 20][-6 + 62] = true
      this.cells[1 + 20][-4 + 62] = true
      this.cells[1 + 20][-3 + 62] = true
      this.cells[1 + 20][6 + 62] = true
      this.cells[1 + 20][11 + 62] = true
      this.cells[1 + 20][12 + 62] = true
      this.cells[1 + 20][13 + 62] = true
      this.cells[2 + 20][-12 + 62] = true
      this.cells[2 + 20][-11 + 62] = true
      this.cells[2 + 20][-10 + 62] = true
      this.cells[2 + 20][-9 + 62] = true
      this.cells[2 + 20][-5 + 62] = true
      this.cells[3 + 20][-13 + 62] = true
      this.cells[3 + 20][-12 + 62] = true
      this.cells[3 + 20][-10 + 62] = true
      this.cells[3 + 20][-9 + 62] = true
      this.cells[4 + 20][-12 + 62] = true
      this.cells[4 + 20][-11 + 62] = true
      this.cells[4 + 20][-2 + 62] = true
      this.cells[4 + 20][1 + 62] = true
      this.cells[5 + 20][-3 + 62] = true
      this.cells[6 + 20][-3 + 62] = true
      this.cells[6 + 20][1 + 62] = true
      this.cells[7 + 20][-3 + 62] = true
      this.cells[7 + 20][-2 + 62] = true
      this.cells[7 + 20][-1 + 62] = true
      this.cells[7 + 20][0 + 62] = true
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
    vertical-align: middle;
  }
  button:hover {
    background: #fff;
    border: 2px solid #8b81fc;
    color: #8b81fc;
  }
  button > img,
  button > span {
    vertical-align: middle;
  }
  .saves > button,
  .saves > textarea {
    vertical-align: middle;
  }
</style>
