<!-- NutritionLabel.vue -->
<template>
  <div class="nutrition-facts">
    <div class="header">
      <!-- <h1>{{ 'Nutrition Facts' }}</h1> -->

      <!-- Serving Information -->
      <div class="serving-info">
        <div>
          {{ servingsPerContainer }}
        </div>
        <div class="serving-size">
          {{ servingSize }}
        </div>
      </div>
    </div>

    <!-- Calories -->
    <div class="calories">
      <div class="calories-label">'Calories'</div>
      <div class="calories-value">{{ Math.round(calories) }}</div>
    </div>

    <!-- Daily Value Notice -->
    <div class="daily-value-header">
      <!-- <div>% {{ isArabic ? 'القيمة اليومية*' : 'Daily Value*' }}</div> -->
    </div>

    <!-- Nutrients -->
    <div class="nutrients">
      <!-- <template v-for="(section, sectionIndex) in groupedNutrients">
        <div :key="sectionIndex" class="nutrient-section">
          <div
            v-for="nutrient in section"
            :key="nutrient.id"
            class="nutrient-row"
            v-show="selectedNutrients[nutrient.id]"
            :style="{ paddingLeft: `${nutrient.indentation * 20}px` }"
          >
            <div class="nutrient-name">{{ nutrient.name }}</div>
            <div class="nutrient-value">
              {{ Math.round(nutrient.value) }}{{ nutrient.unit || 'g' }}
            </div>
            <div class="daily-value" v-if="nutrient.dailyValue">
              {{ Math.round((nutrient.value / nutrient.dailyValue) * 100) }}%
            </div>
          </div>
        </div>
      </template> -->
    </div>

    <!-- Disclaimer -->
    <div class="disclaimer">
      <p>
        'The % Daily Value (DV) tells you how much a nutrient in a serving of
        food contributes to a daily diet. 2,000 calories a day is used for
        general nutrition advice.'
      </p>
    </div>

    <!-- Nutrient Toggle Controls -->
    <div class="nutrient-toggles">
      <h3>'Customize Nutrients'</h3>
      <div
        v-for="nutrient in allNutrients"
        :key="nutrient.id"
        class="toggle-row"
      >
        <label>
          <input type="checkbox" v-model="selectedNutrients[nutrient.id]" />
          {{ nutrient.name }}
        </label>
      </div>
    </div>

    <!-- Language Toggle -->
    <div class="language-toggle">
      <button @click="toggleLanguage">
        <!-- {{ isArabic ? 'Switch to English' : 'التبديل إلى العربية' }} -->
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'NutritionLabel',
  data() {
    return {
      isArabic: false,
      servingsPerContainer: '8',
      servingSize: '2/3 cup (55g)',
      calories: 230,
      selectedNutrients: {},
      // Sample nutrient data - replace with API data
      nutrients: [
        {
          id: 1,
          name: 'Total Fat',
          value: 8,
          unit: 'g',
          dailyValue: 78,
          enabled: 1,
          section: 1,
          order: 1,
          indentation: 0,
        },
        {
          id: 2,
          name: 'Saturated Fat',
          value: 1,
          unit: 'g',
          dailyValue: 20,
          enabled: 1,
          section: 1,
          order: 2,
          indentation: 1,
        },
        // Add more nutrients as needed
      ],
    };
  },
  computed: {
    allNutrients() {
      console.log(this.nutrients);
      return this.nutrients;
    },
    groupedNutrients() {
      const grouped = {};
      this.nutrients.forEach((nutrient) => {
        if (!grouped[nutrient.section]) {
          grouped[nutrient.section] = [];
        }
        grouped[nutrient.section].push(nutrient);
      });

      // Sort nutrients within each section
      Object.values(grouped).forEach((section) => {
        section.sort((a, b) => a.order - b.order);
      });

      return grouped;
    },
  },
  methods: {
    initializeSelectedNutrients() {
      this.nutrients.forEach((nutrient) => {
        this.$set(this.selectedNutrients, nutrient.id, nutrient.enabled === 1);
      });
    },
  },
  created() {
    this.initializeSelectedNutrients();
  },
};
</script>

<style scoped>
.nutrition-facts {
  font-family: 'Roboto', sans-serif;
  max-width: 300px;
  padding: 0.5rem;
  border: 1px solid #000;
}

.rtl {
  direction: rtl;
  font-family: 'Noto Sans Arabic', sans-serif;
}

.header {
  border-bottom: 10px solid #000;
  padding-bottom: 0.5rem;
}

.header h1 {
  font-size: 2rem;
  margin: 0;
  padding: 0;
}

.serving-info {
  font-size: 1.2rem;
  margin-top: 0.5rem;
}

.serving-size {
  font-size: 1.4rem;
  font-weight: bold;
}

.calories {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.5rem 0;
  border-bottom: 4px solid #000;
}

.calories-label {
  font-size: 1.2rem;
}

.calories-value {
  font-size: 2rem;
  font-weight: bold;
}

.daily-value-header {
  font-size: 0.9rem;
  border-bottom: 1px solid #000;
  padding: 0.3rem 0;
}

.nutrient-section {
  border-bottom: 4px solid #000;
}

.nutrient-row {
  display: flex;
  justify-content: space-between;
  padding: 0.2rem 0;
  border-bottom: 1px solid #000;
}

.nutrient-name {
  flex: 2;
}

.nutrient-value {
  flex: 1;
  text-align: right;
}

.daily-value {
  flex: 1;
  text-align: right;
}

.disclaimer {
  font-size: 0.8rem;
  margin-top: 0.5rem;
}

.nutrient-toggles {
  margin-top: 2rem;
  padding: 1rem;
  border: 1px solid #ccc;
}

.toggle-row {
  margin: 0.5rem 0;
}

.language-toggle {
  margin-top: 1rem;
  text-align: center;
}

button {
  padding: 0.5rem 1rem;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3;
}
</style>
