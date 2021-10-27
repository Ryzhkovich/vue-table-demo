<template>
  <td
    v-if="tablePart === 'head'"
    @click="returnCheck(numberKey)"
    :class="`sorter-header-icon ${getCurrentCol()}`"
    :rowspan="data.rowspan"
    :colspan="data.colspan"
    :data-memory-column="memoryColumn"
    :data-sort-descending="sortDescending"
    :data-current-index="numberKey"
    :style="marginRightFunc(data)"
  >
    <div
      v-if="data.function === undefined"
    >
      <span
        v-html="data.value"
      ></span>
    </div>
    <div
      v-else
    >
      <span
        v-html="data.function.value"
      ></span>
      <i
        v-if="data.function.function[0] === 'getSomething'"
        class="fa fa-question-circle-o"
        :data-tooltip="data.function.function[1]"
        style="font-size:20px; margin-left:10px; cursor:help;"
        aria-hidden="true"
      ></i>
    </div>
  </td>
  <td
    v-if="tablePart === 'body'"
    :rowspan="data.rowspan"
    :colspan="data.colspan"
  >
    <span
      v-if="numberKey === 0 && repType === 'rep_pbnd'"
      v-html="data.value"
      :data-value="data.value"
      :data-index="numberKey"
      :data-rep-type="repType"
    ></span>
    <span
      v-else
      v-html="prettify(data.value)"
      :data-value="parseFloat(data.value)"
      :data-index="numberKey"
      :data-rep-type="repType"
    ></span>
  </td>
  <td
    v-if="tablePart === 'footer'"
    :rowspan="data.rowspan"
    :colspan="data.colspan"
  >
    <span
      v-if="data.function === undefined"
      v-html="prettify(data.value)"
    >
    </span>
  </td>
</template>

<script>
// import moment from "moment";

export default {
  name: "BigCell",
  components: {},
  props: {
    data: {
      type: [Object, String],
      required: true
    },
    tablePart: {
      type: String,
      required: true
    },
    numberKey: {
      type: [String, Number],
      required: false
    },
    repType: {
      type: String,
      required: false,
      default: ''
    },
    memoryColumn: {
      type: [Number, String],
      required: false,
      default: null
    },
    sortDescending: {
      type: Boolean,
      required: false,
      default: null
    }
  },
  beforeCreate() {
    this.$options.components.TableBody = require("../TableBody/TableBody.vue").default;
    this.$options.components.Row = require("../Rows/Row.vue").default;
  },
  data() {
    return {
      cellFunction: '',
      currentColumn: 0,
      currentSort: false
    };
  },
  methods: {
    getCurrentCol() {
      if (this.numberKey === this.memoryColumn) {
        return (this.sortDescending ? 'sorter-headerDesc-icon' : 'sorter-headerAsc-icon');
      } else {
        return '';
      }
    },
    momentFunc() {
      // return moment(prop, 'YYYY-MM-DD').format('DD.MM.YYYY');
      return false;
    },
    prettify(num) {
      let n = num.toString();
      let reg = new RegExp('^[0-9]+$');
      if (reg.test(num)) {
        return n.replace(/(\d{1,3}(?=(?:\d\d\d)+(?!\d)))/g, "$1" + ' ');
      } else {
        return num;
      }
    },
    returnCheck(col) {
      return this.$emit('change-data', col);
    },
    print(value){
      if (isNaN(value)) return value;
      let r = '';
      value = (value + '');
      let j = 0;
      for(let i = value.length-1;i>=0;i--) {
        r = value[i] + r;
        if (j % 3 === 2)
          r = ' ' + r;
        j++;
      }
      return r;
    },
    getSomething(prop=false) {
      return prop;
    },
    marginRightFunc(data) {
      if (data.function !== undefined) {
        return 'text-align: left; padding: 0;'
      }
    }
  },
  beforeMount() {
    if (this.data.function !== undefined) {
      this.cellFunction = this[this.data.function.function[0]](this.data);
    }
  }
}
</script>

<style lang="scss" scoped>
th {
  border: 2px solid #2c3e50;
  span {
    color: cadetblue;
  }
}
</style>