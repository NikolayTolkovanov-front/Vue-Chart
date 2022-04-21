<template>
  <div id="app">
    <j-header></j-header>
    <j-start></j-start>
    <j-footer></j-footer>
  </div>
</template>

<script>
import JHeader from '@/components/Header'
import JFooter from '@/components/Footer'
import JStart from '@/pages/Start'

export default {
  components: { JHeader, JFooter, JStart },
  name: 'app'
}
</script>

<style lang="scss">
  @import "/assets/styles/_variables.scss";

  .Cookie__button {
    background: color(robin-egg-blue)!important;

    @include hover-active-states() {
      background: darken(color(robin-egg-blue), 10%)!important;
    }

  }
</style>
