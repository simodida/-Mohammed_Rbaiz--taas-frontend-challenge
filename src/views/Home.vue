<template>
  <div class="home">
    <convert-currency />
  </div>
</template>

<script lang="ts">
import Vue from "vue";

import ConvertCurrency from "../components/convert.vue";

export default Vue.extend({
  name: "Home",
  components: {
    ConvertCurrency,
  },
});
</script>

<style lang="scss"></style>
