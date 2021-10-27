<template>
  <tr
    v-for="(items, indexRow) in data" :key="indexRow"
    :class="`row-${tablePart}`"
    role="row"
  >
    <BigCell
      v-for="(item, index) in items" :key="item"
      :data="item"
      :number-key="index"
      :rep-type="repType"
      :table-part="tablePart"
      :memory-column="memoryColumn"
      :sort-descending="sortDescending"
      @change-data='changeData(index)'
    />
  </tr>
</template>

<script>
import BigCell from "../BigCells/BigCell.vue";

export default {
  name: "Row",
  components: {BigCell},
  props: {
    data: {
      type: [Array, Object, String],
      required: true
    },
    numberKey: {
      type: [Number, String],
      required: false
    },
    tablePart: {
      type: String,
      required: true,
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
  data() {
    return {
    };
  },
  methods: {
    changeData(col) {
      if (this.tablePart === 'head') {
        this.$emit('change-sort', col);
      }
    },
  },
}
</script>

<style scoped>
tr.row-head td{
  background-color: #ebe8e2 !important;
}
tr.row-body {
  width: 100%;
}
tr.row-footer {
  background-color:  #ebe8e2 !important;
}
</style>