<template>
  <div class="Results">
    <div class="Results__overallData title">
      <p class="Results__overallData-title">Общие свойства</p>
      <p class="Results__overallData-epsilon">epsilon: {{ overallData[0] }}</p>
      <p class="Results__overallData-teoretic-steps">teoreticSteps: {{ overallData[1] }}</p>
    </div>
    <hr>
    <hr>
    <span class="title">Dihotomy:</span>
    <ul class="Results__list">
      <li class="Results__list-item" v-for="item in dihotomyData" :key="item.id">
        <results-card :items="item"></results-card>
      </li>
    </ul>
    <hr>
    <span class="title">Hord:</span>
    <ul class="Results__list">
      <li class="Results__list-item" v-for="item in hordData" :key="item.id">
        <results-card :items="item"></results-card>
      </li>
    </ul>
    <hr>
    <span class="title">Newton:</span>
    <ul class="Results__list">
      <li class="Results__list-item" v-for="item in newtonData" :key="item.id">
        <results-card :items="item"></results-card>
      </li>
    </ul>
  </div>
</template>
<script>
import ResultsCard from './ResultsCard'

export default {
  components: { ResultsCard },
  props: {
    overallData: {
      type: Array
    },

    dihotomyData: {
      type: Array
    },

    hordData: {
      type: Array
    },

    newtonData: {
      type: Array
    }
  },

  computed: {}
}
</script>
<style scoped lang="scss">

.Results__list {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
}

.title {
  font-size: 20px;
}
</style>
