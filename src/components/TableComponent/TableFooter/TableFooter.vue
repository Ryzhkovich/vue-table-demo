<template>
  <tfoot class="position-align">
    <Row
      v-for="(item) in objFooter" :key="item"
      :data="item"
      :table-part="componentType"
    />
  </tfoot>
</template>

<script>
import Row from "../Rows/Row.vue";

export default {
  name: "TableFooter",
  components: {Row},
  props: {
    objFooter: {
      type: Object,
      required: true
    },
  },
  data() {
    return {
      componentType: 'footer'
    };
  }
}
</script>

<style scoped>
</style>