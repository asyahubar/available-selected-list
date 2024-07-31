<template>
  <SelectItemsWidget
    v-model:selected="selected"
    :available
  />
</template>

<script setup>
import { ref } from 'vue'

import SelectItemsWidget from './components/SelectItemsWidget.vue'

// DATA
const available = ref([])
available.value = Array.from({ length: 300 }, (item, ind) => `Element ${ind+1}`)

const selected = ref([
  available.value[2],
  available.value[81],
  available.value[222]
])

</script>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
