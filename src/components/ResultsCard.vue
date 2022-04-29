<template>
  <ul class="results-card">
    <li class="results-card-item">
      x: {{ items[0] }}
    </li>
    <li class="results-card-item">
      f(x): {{ items[1] }}
    </li>
    <li class="results-card-item">
      steps: {{ items[2] }}
    </li>
  </ul>
</template>

<script>
export default {
  props: {
    items: {
      type: Array
    }
  },

  mounted () {
    console.log(this.items)
  }
}
</script>

<style></style>
