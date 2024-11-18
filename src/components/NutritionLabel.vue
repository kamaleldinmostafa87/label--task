<!-- NutritionLabel.vue -->
<template>
  <div class="nutrition-facts-container">
    <!-- Main Nutrition Label -->
    <div class="nutrition-label">
      <div class="label-container">
        <h1 class="label-title">Nutrition Facts</h1>

        <!-- Servings Information -->
        <div class="servings-info">
          <p>{{ label.amounts.servingsPerContainer }} servings per container</p>
          <div class="serving-size">
            <span class="bold">Serving size</span>
            <span>{{ label.amounts.servingSize }}</span>
          </div>
        </div>

        <!-- Calories -->
        <div class="calories-container">
          <div class="calories-row">
            <span class="calories-label">Calories</span>
            <span class="calories-value">{{
              Object.values(label.serving).find(
                (item) => item.name === "Calories"
              ).value
            }}</span>
          </div>
        </div>

        <!-- Daily Value Header -->
        <div class="daily-value-header">
          <span>% Daily Value*</span>
        </div>

        <div v-for="section in getSortedSections" :key="section">
          <div class="flex justify-between">
            <div>
              <span>
                {{ section.name }}
              </span>

              <span
                >{{ Math.round(section.value) }}
                {{
                  section.unit !== null ? Math.round(section.unit.grams) : "g"
                }}</span
              >
            </div>

            <div>{{ section.daily_value }} %</div>
          </div>
        </div>

        <!-- Nutrients Sections -->
        <!--<div
          v-for="section in getSortedSections"
          :key="section"
          class="nutrient-section"
        >
          <div
            v-for="nutrient in getNutrientsBySection(section)"
            :key="nutrient.name"
            class="nutrient-row"
            v-show="visibleNutrients[nutrient.name]"
            :style="getIndentationStyle(nutrient)"
          >
            <span class="nutrient-name">{{ nutrient.name }}</span>
            <div class="nutrient-values">
              <span class="amount">
                {{ formatValue(nutrient.value) }}{{ nutrient.unit || "g" }}
              </span>
              <span v-if="nutrient.daily_value" class="daily-value">
                {{ calculateDailyValue(nutrient) }}
              </span>
            </div>
          </div>
        </div> -->

        <!-- Disclaimer -->
        <div class="disclaimer">
          * The % Daily Value tells you how much a nutrient in a serving of food
          contributes to a daily diet. 2,000 calories a day is used for general
          nutrition advice.
        </div>
      </div>
    </div>

    <!-- Nutrient Toggle Controls -->
    <div class="nutrient-controls">
      <h2>Customize Visible Nutrients</h2>
      <div class="toggle-container">
        <label
          v-for="nutrient in label.serving"
          :key="nutrient.name"
          class="toggle-label"
        >
          <input type="checkbox" v-model="visibleNutrients[nutrient.name]" />
          {{ nutrient.name }}
        </label>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "NutritionLabel",

  props: {
    label: {
      type: Object,
      required: true,
      // default: () => ({
      //   amounts: {
      //     servingsPerContainer: 0,
      //     servingSize: "",
      //   },
      //   serving: [],
      //   daily_values: {},
      // }),
    },
  },

  data() {
    return {
      visibleNutrients: {},
      isRTL: false,
    };
  },

  computed: {
    // getCalories() {
    //   const caloriesNutrient = this.label.serving.find(
    //     (n) => n.name === "Calories"
    //   );
    //   return this.formatValue(caloriesNutrient?.value || 0);
    // },

    getSortedSections() {
      //remove repeated values if exist
      const sections = new Set(Object.values(this.label.serving));
      //get enabled only
      return [...sections].filter((section) => section.enabled === 1);
    },
  },

  methods: {
    formatValue(value) {
      return Math.round(value);
    },

    getNutrientsBySection(section) {
      return this.label.serving.filter((n) => n.section === section);
      // .sort((a, b) => a.order - b.order);
    },

    calculateDailyValue(nutrient) {
      if (!nutrient.daily_value) return null;
      return this.formatValue((nutrient.value / nutrient.daily_value) * 100);
    },

    getIndentationStyle(nutrient) {
      return nutrient.indentation
        ? { marginLeft: `${nutrient.indentation * 20}px` }
        : {};
    },

    initializeVisibleNutrients() {
      //   this.visibleNutrients = Object.values(this.label.serving).reduce(
      //     (acc, nutrient) => {
      //       acc[nutrient.name] = nutrient.enabled === 1;
      //       return acc;
      //     },
      //     {}
      //   );
      this.visibleNutrients = Object.values(this.label.serving);

      // console.log(
      //   Array.from(Object.entries(this.label.serving), ([key, value]) => {
      //     return value;
      //   })
      // );
      const sections = new Set(
        Array.from(Object.entries(this.label.serving), ([key, value]) => {
          return value;
        })
      );
      console.log(sections);
    },
  },

  created() {
    this.initializeVisibleNutrients();
  },
};
</script>

<style scoped>
.nutrition-facts-container {
  display: flex;
  flex-direction: column;
  gap: 2rem;
  font-family: "Roboto", sans-serif;
}

.nutrition-label {
  width: 320px;
  background: white;
  padding: 1rem;
}

.label-container {
  border: 2px solid black;
  padding: 1rem;
}

.label-title {
  font-size: 2rem;
  font-weight: bold;
  margin-bottom: 0.25rem;
}

.servings-info {
  border-bottom: 8px solid black;
  padding: 0.5rem 0;
}

.serving-size {
  display: flex;
  justify-content: space-between;
}

.calories-container {
  border-bottom: 8px solid black;
  padding: 0.5rem 0;
}

.calories-row {
  display: flex;
  justify-content: space-between;
  align-items: baseline;
}

.calories-label {
  font-size: 1.25rem;
  font-weight: bold;
}

.calories-value {
  font-size: 2.5rem;
  font-weight: bold;
}

.daily-value-header {
  text-align: right;
  font-size: 0.875rem;
  padding: 0.25rem 0;
  border-bottom: 1px solid black;
}

.nutrient-section {
  border-top: 2px solid black;
  padding: 0.5rem 0;
}

.nutrient-row {
  display: flex;
  justify-content: space-between;
  padding: 0.25rem 0;
}

.nutrient-values {
  display: flex;
  gap: 1rem;
}

.bold {
  font-weight: bold;
}

.disclaimer {
  font-size: 0.75rem;
  margin-top: 1rem;
}

.nutrient-controls {
  padding: 1rem;
  background: white;
  border-radius: 0.5rem;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}

.nutrient-controls h2 {
  font-size: 1.25rem;
  font-weight: bold;
  margin-bottom: 1rem;
}

.toggle-container {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 0.5rem;
}

.toggle-label {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

/* RTL Support */
.rtl {
  direction: rtl;
}
</style>
