<template>
  <div class="hello">
    {{ this.turn }}
    <table class="board" cellspacing="0" cellpadding="0">
      <tr v-for="(row, rowIndex) in boards" :key="rowIndex">
        <td
          v-for="(col, colIndex) in row"
          :key="colIndex"
          v-on:click="placeOrder(rowIndex, colIndex)"
          :class="boards[rowIndex][colIndex] == 'x' ? 'red' : 'green'"
        >
          {{ boards[rowIndex][colIndex] }}
        </td>
      </tr>
    </table>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },
  data: function() {
    return {
      m: 10,
      n: 10,
      boards: [],
      turn: 0,
      isEndGame: false,
      xArray: [],
      oArray: [],
    };
  },
  created: function() {
    for (let i = 0; i < this.m; i++) {
      var cols = [];
      for (let j = 0; j < this.n; j++) {
        cols.push("");
      }

      this.boards.push(cols);
    }
  },
  methods: {
    placeOrder: function(rowIndex, colIndex) {
      if (this.isEndGame) {
        return;
      }

      if (
        this.boards[rowIndex][colIndex] == "x" ||
        this.boards[rowIndex][colIndex] == "o"
      ) {
        return;
      }

      var char = "";
      if (this.turn % 2 == 0) {
        char = "x";
        this.xArray.push({ row: rowIndex, col: colIndex });
      } else {
        char = "o";
        this.oArray.push({ row: rowIndex, col: colIndex });
      }

      if (char == "x") {
        console.log(rowIndex, colIndex);
      }

      this.boards[rowIndex][colIndex] = char;
      this.turn++;

      var winner = this.isWinner(char);
      if (winner) {
        this.isEndGame = true;
      }
    },
    isWinner: function(player) {
      var source = [];
      if (player == "x") {
        source = this.xArray;
      } else {
        source = this.oArray;
      }

      var isWinner = this.checkWinnerByRow(source);
      if (isWinner) {
        return true;
      }

      isWinner = this.checkWinnerByCol(source);
      if (isWinner) {
        return true;
      }

      isWinner = this.checkWinnerByDiagonalLine(source);
      return isWinner;
    },
    checkWinnerByRow: function(source) {
      var count = 0;
      var rowGroups = source.reduce((groups, item) => {
        const group = groups[item.row] || [];
        group.push(item);
        groups[item.row] = group;
        return groups;
      }, {});

      for (let t = 0; t < Object.values(rowGroups).length; t++) {
        const gr = Object.values(rowGroups)[t];
        let sortArr = gr.sort(function(a, b) {
          return a.col - b.col;
        });

        for (let i = 0; i < sortArr.length - 1; i++) {
          if (parseInt(sortArr[i].col) + 1 == parseInt(sortArr[i + 1].col)) {
            count++;
          } else {
            count = 0;
          }
        }

        if (count >= 4) {
          return true;
        } else {
          count = 0;
        }
      }

      return count >= 4;
    },
    checkWinnerByCol: function(source) {
      var count = 0;
      var colGroups = source.reduce((groups, item) => {
        const group = groups[item.col] || [];
        group.push(item);
        groups[item.col] = group;
        return groups;
      }, {});

      for (let t = 0; t < Object.values(colGroups).length; t++) {
        const gr = Object.values(colGroups)[t];
        let sortArr = gr.sort(function(a, b) {
          return a.row - b.row;
        });

        for (let i = 0; i < sortArr.length - 1; i++) {
          if (parseInt(sortArr[i].row) + 1 == parseInt(sortArr[i + 1].row)) {
            count++;
          } else {
            count = 0;
          }
        }

        if (count >= 4) {
          return true;
        } else {
          count = 0;
        }
      }

      return count >= 4;
    },
    checkWinnerByDiagonalLine: function(source) {
      var count = 0;
      let sortArr = source.sort(function(a, b) {
        return a.row - b.row;
      });

      for (let t = 0; t < sortArr.length - 1; t++) {
        if (parseInt(sortArr[t].row) == parseInt(sortArr[t + 1].row)) {
          // Do nothing
        } else if (
          parseInt(sortArr[t].col) + 1 ==
          parseInt(sortArr[t + 1].col)
        ) {
          count++;
        } else {
          count = 0;
        }
      }

      if (count >= 4) {
        return true;
      }

      count = 0;
      for (let t = 0; t < sortArr.length - 1; t++) {
        if (parseInt(sortArr[t].row) == parseInt(sortArr[t + 1].row)) {
          // Do nothing
        } else if (
          parseInt(sortArr[t].col) ==
          parseInt(sortArr[t + 1].col) + 1
        ) {
          count++;
        } else {
          count = 0;
        }
      }

      return count >= 4;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.board {
  margin: 50px auto;
  border-spacing: 5px;
  border-spacing: 0;
  border-collapse: collapse;
}
.board tr {
  border: 1px solid #ccc;
}
.board tr td {
  padding: 3px;
  border: 1px solid #ccc;
  width: 30px;
  height: 30px;
}
.board tr td:hover {
  cursor: pointer;
  background: #ccc;
}

.red {
  color: red;
  font-weight: bold;
}

.green {
  color: green;
  font-weight: bold;
}
</style>
