<template>
  <ul class="Result-list">
    <li>x: {{ xValue }}</li>
    <li>y: {{ yValue }}</li>
    <li>epsilon: {{ epsilon }}</li>
    <li>steps: {{ steps }}</li>
      <!-- <li>{{ teorSteps }}</li> -->
  </ul>
</template>
<script>
export default {
  props: {
    xValue: { type: Number },
    yValue: { type: Number },
    epsilon: { type: Number },
    steps: { type: Number }
    // teorSteps: { type: Number }
  },

  computed: {
    normalizedXValues () {
      return this.xValue.toPresition
    }
  }
}
</script>
<style scoped lang="scss">

</style>
