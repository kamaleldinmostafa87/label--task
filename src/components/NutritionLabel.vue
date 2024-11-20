<!-- NutritionLabel.vue -->
<template>
  <div
    class="nutrition-facts-container"
    :dir="currentLanguage === 'ar' ? 'rtl' : 'ltr'"
  >
    <div class="nutrition-label">
      <div class="label-container">
        <h1 class="label-title">
          {{ currentLanguage === "ar" ? "حقائق غذائية" : "Nutrition Facts" }}
        </h1>

        <!-- Servings Information -->
        <div class="servings-info">
          <p>
            {{ label.amounts.number_of_servings }}
            {{
              currentLanguage === "ar"
                ? "حصة في العبوة"
                : "servings per container"
            }}
          </p>
          <div class="serving-size">
            <span class="bold">{{
              currentLanguage === "ar" ? "حجم الحصة" : "Serving size"
            }}</span>
            <span>{{ formatServingSize }}</span>
          </div>
        </div>

        <!-- Energy Section -->
        <div class="calories-container">
          <div
            v-for="(nutrient, key) in energyNutrients"
            :key="key"
            class="energy-row"
          >
            <span class="energy-label">{{
              getCurrentLanguageName(nutrient)
            }}</span>

            <span>
              {{ formatValue(nutrient?.value) }}
              {{ nutrient?.unit?.name }}</span
            >
          </div>
        </div>

        <!-- Daily Value Header -->
        <div class="daily-value-header">
          <span>{{
            currentLanguage === "ar" ? "% القيمة اليومية*" : "% Daily Value*"
          }}</span>
        </div>

        <!-- Nutrients -->
        <div class="nutrients-container">
          <div
            v-for="(nutrient, key) in sortedNutrients"
            :key="key"
            class="nutrient-row"
            v-show="nutrient.enabled"
            :style="getIndentationStyle(nutrient)"
          >
            <span class="nutrient-name">{{
              getCurrentLanguageName(nutrient)
            }}</span>
            <div class="nutrient-values">
              <span class="amount">
                {{ Math.round(nutrient.value) }}
                {{ nutrient?.unit?.name || "g" }}
              </span>
              <span v-if="getDailyValue(key)" class="daily-value-percent">
                {{ calculateDailyValue(key, nutrient.value) }}%
              </span>
            </div>
          </div>
        </div>

        <!-- Disclaimer -->
        <div class="disclaimer">
          {{
            currentLanguage === "ar"
              ? "* النسبة المئوية للقيمة اليومية تخبرك عن مقدار المغذيات في حصة من الطعام التي تساهم في النظام الغذائي اليومي. 2000 سعرة حرارية في اليوم تستخدم للنصائح الغذائية العامة."
              : "* The % Daily Value tells you how much a nutrient in a serving of food contributes to a daily diet. 2,000 calories a day is used for general nutrition advice."
          }}
        </div>
      </div>
    </div>

    <!-- Language Toggle -->
    <div class="controls-section">
      <button @click="toggleLanguage" class="language-toggle">
        {{ currentLanguage === "en" ? "عربي" : "English" }}
      </button>

      <!-- Nutrient Toggle Controls -->
      <div class="nutrient-controls">
        <h2>
          {{
            currentLanguage === "ar"
              ? "تخصيص المغذيات المرئية"
              : "Customize Visible Nutrients"
          }}
        </h2>
        <div class="toggle-container">
          <label
            v-for="(nutrient, key) in label.serving"
            :key="key"
            class="toggle-label"
          >
            {{ console.log(label.serving) }}
            <input type="checkbox" @change="toggleNutrient(key)" />
            {{ getCurrentLanguageName(nutrient) }}
          </label>
        </div>
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
    },
  },

  data() {
    return {
      currentLanguage: "en",
    };
  },

  computed: {
    energyNutrients() {
      return (
        Object.entries(this.label.serving)
          //[[key,value],[key,value]]
          .filter(([key]) => key.includes("Energy"))
          //[[key,value]]
          .reduce((acc, [key, value]) => {
            // {key: value}
            acc[key] = value;
            return acc;
          }, {})
      );
    },

    sortedNutrients() {
      return (
        Object.entries(this.label.serving)
          .filter(([key]) => !key.includes("Energy"))
          .sort((a, b) => {
            // Sort by section first, then by order
            if (a[1].section !== b[1].section) {
              return a[1].section - b[1].section;
            }
            return a[1].order - b[1].order;
          })
          //sorted array then get the object will be iterated using for..in
          .reduce((acc, [key, value]) => {
            acc[key] = value;
            return acc;
          }, {})
      );
    },

    formatServingSize() {
      return `${this.label.amounts.serving}g`;
    },
  },

  methods: {
    formatValue(value) {
      return Math.round(value);
    },
    compare(a, b) {
      return a - b;
    },
    getCurrentLanguageName(nutrient) {
      return this.currentLanguage === "ar" ? nutrient.name_ar : nutrient.name;
    },

    getDailyValue(nutrientName) {
      return this.label.daily_value[nutrientName] || 0;
    },

    calculateDailyValue(nutrientName, value) {
      const dailyValue = this.getDailyValue(nutrientName);
      if (!dailyValue) return 0;
      return this.formatValue((value / dailyValue) * 100);
    },

    getIndentationStyle(nutrient) {
      return nutrient.indentations
        ? { marginLeft: `${nutrient.indentations * 20}px` }
        : {};
    },

    toggleLanguage() {
      this.currentLanguage = this.currentLanguage === "en" ? "ar" : "en";
    },

    toggleNutrient(nutrientKey) {
      this.$set(
        this.label.serving[nutrientKey],
        "enabled",
        !this.label.serving[nutrientKey].enabled
      );
    },
  },
};
</script>

<style scoped>
.nutrition-facts-container {
  font-family: "Roboto", sans-serif;
  max-width: 380px;
  margin: 0 auto;
}

.nutrition-label {
  background: white;
  padding: 1rem;
  margin-bottom: 2rem;
}

.label-container {
  border: 2px solid black;
  padding: 0.75rem;
}

.label-title {
  font-size: 2rem;
  font-weight: bold;
  margin-bottom: 0.5rem;
}

.servings-info {
  border-bottom: 8px solid black;
  padding: 0.5rem 0;
}

.serving-size {
  display: flex;
  justify-content: space-between;
  font-size: 1.2rem;
}

.calories-container {
  border-bottom: 8px solid black;
  padding: 0.5rem 0;
}

.energy-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 0.25rem 0;
}

.energy-label {
  font-size: 1.2rem;
  font-weight: bold;
}

.energy-value {
  font-size: 2rem;
  font-weight: bold;
}

.daily-value-header {
  text-align: right;
  padding: 0.25rem 0;
  border-bottom: 1px solid black;
}

.nutrient-row {
  display: flex;
  justify-content: space-between;
  padding: 0.25rem 0;
  border-bottom: 1px solid #ddd;
}

.nutrient-values {
  display: flex;
  gap: 1rem;
}

.controls-section {
  padding: 1rem;
  background: #f5f5f5;
  border-radius: 8px;
}

.language-toggle {
  margin-bottom: 1rem;
  padding: 0.5rem 1rem;
  background: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.nutrient-controls {
  margin-top: 1rem;
}

.toggle-container {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 0.5rem;
}

.toggle-label {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.disclaimer {
  font-size: 0.75rem;
  margin-top: 1rem;
}

[dir="rtl"] {
  font-family: "Noto Sans Arabic", sans-serif;
}
</style>
