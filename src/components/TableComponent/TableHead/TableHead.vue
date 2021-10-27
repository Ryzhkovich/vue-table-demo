<template>
  <thead
    class="sticky-head position-align"
  >
    <Row
      v-for="(item) in objHead" :key="item"
      :data="item"
      :table-part="componentType"
      :memory-column="memoryColumn"
      :sort-descending="sortDescending"
      @change-sort="getColl"
    />
  </thead>
</template>

<script>
import Row from "../Rows/Row.vue";

export default {
  name: "TableHead",
  components: {Row},
  props: {
    objHead: {
      type: Object,
      required: true
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
      componentType: 'head'
    };
  },
  methods: {
    getColl(col) {
      this.$emit('change-table-sort', col);
    }
  },
}
</script>

<style scoped>
</style>