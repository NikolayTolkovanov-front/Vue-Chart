<template>
  <div class="Header">
    <span class="Header__logo">
      <h1>Пожилые методы</h1>
    </span>
  </div>
</template>

<script>
  export default {

  }
</script>

<style lang="scss">
  @import "../assets/styles/_variables.scss";

  .Header {
    padding: rem(30) rem(40);
    display: flex;
    justify-content: center;

    @include has(logo) {
      font-size: rem(20);
      font-weight: 600;
    }
  }
</style>
