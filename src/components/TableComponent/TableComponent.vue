<template>
  <div class="overflow">
    <table
      class="component-vue-table right sticky-head"
      style="width: 10%;"
      :id="`wrapper-${repid}`"
    >
      <TableHead
        :obj-head="tableHead"
        :memory-column="memoryColumn"
        :sort-descending="sortDescending"
        @change-table-sort="TableSort"
      />
      <TableBody
        :obj-body="tableData"
        :rep-type ="repid.replace(/\d/g, '')"
      />
      <TableFooter :obj-footer="tableFooter"/>
    </table>
  </div>
</template>

<script>
import TableBody from "./TableBody/TableBody.vue";
import TableHead from "./TableHead/TableHead.vue";
import TableFooter from "./TableFooter/TableFooter.vue";
// import {mapState} from 'vuex';

export default {
  name: 'TableComponent',
  components: {
    TableHead,
    TableBody,
    TableFooter
  },
  props: {
    msg:  {
      type: [String],
      required: false,
      default: ''
    },
    renderTable:  {
      type: [String],
      required: false,
      default: ''
    },
    headProp: {
      type: [Object, Array],
      required: false,
      default: null
    },
    bodyProp: {
      type: [Object, Array],
      required: false,
      default: null
    },
    footerProp: {
      type: [Object, Array],
      required: false,
      default: null
    },
    repid: {
      type: String,
      required: false,
    }
  },
  data() {
    return {
      tableHead: {},
      tableData: {},
      tableFooter: {},
      dynStatus: true,
      memoryColumn: '0',
      sortDescending: false,
    }
  },
  /*computed: mapState({
    // headJson: state => state.headJson,
    headJson_2: state => state.headJson_2,
    // bodyJson: state => state.bodyJson,
    bodyJson_2: state => state.bodyJson_2,
    // footJson: state => state.footJson,
    footJson_2: state => state.footJson_2,
  }),*/
  methods: {
    setTableData(data) {
      return this.tableData = data;
    },
    objectSort(obj, func) {
      let array = [];
      for (let i in obj) {
        array.push(obj[i]);
      }
      array = array.sort(func);
      return Object.assign({}, array);
    },
    TableSort(col) {
      if (this.memoryColumn !== col) {
        this.memoryColumn = col;
        this.sortDescending = false;
      } else {
        this.sortDescending = !this.sortDescending;
      }
      let desc = -1;
      if (this.sortDescending)
        desc = 1;
      return this.setTableData(this.objectSort(this.tableData,
          (a,b) => {
            let av = a[Object.keys(a)[0]][col].value;
            let bv = b[Object.keys(b)[0]][col].value;
            let r = av < bv ? desc * 1 :
                av > bv ? desc * -1 :
                    0;
            return r;
          }
      ));
    },
    getHeadJSon() {
      return this.headProp;
    },
    getBodyJSon() {
      return this.bodyProp;
    },
    getFootJSon() {
      return this.footerProp;
    },
    glueTable() {
      let result = [];
      for (let i in arguments) {
        for (let j in arguments[i]) {
          result.push(arguments[i][j]);
        }
      }
      return result;
    },
    unGlueTable(array) {
      let head = {};
      let body = {};
      let foot = {};
      var i;
      let keys = Object.keys(array);
      head[keys[0]] = array[keys[0]];
      foot[keys[keys.length-1]] = array[keys[keys.length-1]];
      let notBody = [keys[0], keys[keys.length-1]];
      for (i in array) {
        if (notBody.indexOf(i) != -1) continue;
        body[i] = array[i];
      }

      return [head, body, foot];
    },
    setMax(max, setCount) {
      if (!max || max < setCount) {
        max = setCount;
      }
      return max;
    },
    initTable(data) {
      let result = {};
      let tableData = [];
      let maxRow = [];
      let maxCol = [];
      tableData = this.getRow(data, 0);
      for (let i in tableData) {
        for (let j in tableData[i]) {
          maxRow[i] = this.setMax(maxRow[i], tableData[i][j].length);
          for (let row in tableData[i][j]) {
            maxCol[j] = this.setMax(maxCol[j], tableData[i][j][row].length);
          }
        }
      }
      let rowTable = 0;
      let colTable = 0;
      let chRowTable = 0;
      for (let i in tableData) {
        colTable = 0;
        result[i] = {};
        result[i][rowTable] = {};
        for (let j in tableData[i]) {
          let row = 0;
          for (let col in tableData[i][j][row]) {
            if (!result[i][rowTable]) {
              result[i][rowTable] = {};
              colTable = 0;
            }
            result[i][rowTable][colTable] =
                {
                  rowspan: tableData[i][j].length > 1 ? 1 : maxRow[i],
                  colspan: tableData[i][j][row].length > 1 ? 1 : maxCol[j],
                  value: tableData[i][j][row][col],
                  function: tableData[i][j][row][col]['function'],
                };
            colTable += tableData[i][j][row].length > 1 ? 1 : maxCol[j];
          }
        }
        colTable = 0;
        chRowTable = 0;
        for (let j in tableData[i]) {
          for (let row in tableData[i][j]) {
            if (!+row) continue;
            if (chRowTable < +row)
              chRowTable = +row;
            for (let col in tableData[i][j][row]) {
              if (!result[i][rowTable + +row]) {
                result[i][rowTable + +row] = {};
                colTable = 0;
              }
              result[i][rowTable + +row][colTable] =
                  {
                    rowspan: tableData[i][j].length > 1 ? 1 : maxRow[i],
                    colspan: tableData[i][j][row].length > 1 ? 1 : maxCol[j],
                    value: tableData[i][j][row][col],
                    function: tableData[i][j][row][col]['function'],
                  };
              colTable += tableData[i][j][row].length > 1 ? 1 : maxCol[j];
            }
          }
        }
        rowTable += +chRowTable + 1;
      }
      return result;
    },
    getRow(data, level) {
      if (level >= 2) return data;
      if (typeof data !== 'object') {
        data = {0: data};
      }
      level++;
      let result = []
      for (let i in data) {
        if (i === 'function') {
          result.push(this.getCol({function:data[i]}, level));
        } else {
          result.push(this.getCol(data[i], level));
        }
      }
      return result;
    },
    getCol(data, level) {
      if (typeof data !== 'object') {
        data = {0: data};
      }
      let result = []
      for (let i in data) {
        if (i === 'function') {
          result.push(this.getRow({function:data[i]}, level));
        } else {
          result.push(this.getRow(data[i], level));
        }
      }
      return result;
    },
    createTable() {
      let array = {};
      array = this.glueTable(
          [this.getHeadJSon()],
          this.getBodyJSon(),
          [this.getFootJSon()]
      );
      // создаем объект объектов(таблицу) с colspan/rowspan
      array = this.initTable(array);
      // создаем из "таблицы" 3 массива с colspan/rowspan
      [
        this.tableHead,
        this.tableData,
        this.tableFooter
      ] = this.unGlueTable(array);
    }
  },
  created() {
    this.createTable();
  },
  watch: {
    renderTable() {
      this.createTable();
    }
  }
}
</script>

<style lang="scss">
table.component-vue-table {
  display: table;
  tfoot.position-align td:first-child{
    text-align:left;
  }
  tfoot.position-align td{
    text-align:right;
    padding: 15px 10px;
  }
  thead.position-align td{
    text-align:left;
    background-color: #ebe8e2;
    padding: 15px 10px;
  }
  tr {
    td {
      white-space: nowrap;
      border: 1px solid #ebe8e2;
    }
    th.sorter-header-icon {
      background-color: #ebe8e2;
      //background-image: url(/img/bg.gif);
      background-repeat: no-repeat;
      background-position: center right;
      cursor: pointer;
      padding-right: 20px !important;
      padding-top: 10px !important;
      padding-bottom: 10px !important;
      div span {
        color: rgb(76, 76, 76);
      }
    }
    th.sorter-header-icon.sorter-headerAsc-icon {
      //background-image: url(/img/desc.gif);
    }
    th.sorter-header-icon.sorter-headerDesc-icon {
      //background-image: url(/img/asc.gif);
    }
    .row-head td{
      background-color: #ebe8e2 !important;
    }
    .row-body {
      width: 100%;
    }
    .row-footer {
      background-color:  #ebe8e2 !important;
    }
  }
}
</style>
