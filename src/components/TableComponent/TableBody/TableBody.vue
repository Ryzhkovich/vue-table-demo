<template>
  <tbody
    class="some-grid"
    aria-live="polite"
    aria-relevant="all"
  >
    <Row
      v-for="item in objBody" :key="item"
      :data="item"
      :table-part="componentType"
      :rep-type="repType"
      @change-sort="changeSort"
    />
  </tbody>
</template>

<script>
import Row from "../Rows/Row.vue";

export default {
  name: "TableBody",
  components: {Row},
  props: {
    objBody: {
      type: Object,
      required: true
    },
    repType: {
      type: String,
      required: false,
      default: ''
    }
  },
  data() {
    return {
      componentType: 'body',
      objectBodyData: {}
    };
  },
  methods: {
    changeSort() {

    },
  },
  computed: {
    doSortValue() {
      return this.numberSort();
    },
    numberSort(value) {
      parseInt(value.replace(/ /g, ''), 10)
      return null;
    }
  },
}
</script>

<style scoped>
</style>