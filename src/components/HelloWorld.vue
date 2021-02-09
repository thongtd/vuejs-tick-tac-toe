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
      m: 20,
      n: 20,
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

      var sortArr = source.sort(function(a, b) {
        return a.row - b.row;
      });

      var count = 0;

      // Check by rows
      for (let i = 0; i < sortArr.length - 1; i++) {
        if (
          sortArr[i].row == sortArr[i + 1].row &&
          parseInt(sortArr[i].col) + 1 == parseInt(sortArr[i + 1].col)
        ) {
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

      // Check by cols
      for (let j = 0; j < sortArr.length - 1; j++) {
        if (
          sortArr[j].row == sortArr[j + 1].row &&
          parseInt(sortArr[j].col) + 1 == parseInt(sortArr[j + 1].col)
        ) {
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

      // Case in diagonal
      for (let t = 0; t < sortArr.length - 1; t++) {
        if (parseInt(sortArr[t].row) + 1 == parseInt(sortArr[t + 1].row)) {
          count++;
        } else if (parseInt(sortArr[t].row) == parseInt(sortArr[t + 1].row)) {
          // Do nothing
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
