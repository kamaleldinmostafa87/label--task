<!-- App.vue -->
<template>
  <div id="app">
    <NutritionLabel :label="response.label" />
  </div>
</template>

<script>
import NutritionLabel from "./components/NutritionLabel.vue";
import response from "./response.json";

export default {
  name: "App",

  components: {
    NutritionLabel,
  },

  data() {
    return {
      response,
    };
  },
};
</script>

<style>
#app {
  font-family: "Roboto", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  padding: 2rem;
}

/* Add these imports in your index.html or main entry file */
@import url("https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap");
@import url("https://fonts.googleapis.com/css2?family=Noto+Sans+Arabic:wght@400;700&display=swap");
</style>
