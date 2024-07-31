<template>
  <div 
    id="editor" 
    class="editor"
    aria-labelledby="editor-title"
    aria-hidden="true"
  >
    <div data-a11y-dialog-hide class="editor__overlay"></div>

    <div 
      role="document" 
      class="editor__container"
      data-a11y-dialog="editor"
    >
      <header class="flex editor__header">
        <h2 id="editor-title">Select items</h2>
        <button 
          type="button"
          class="close-button"
          data-a11y-dialog-hide
          aria-label="Close editor"
          @click="emit('close')"
        >
          &#10005;
        </button>
      </header>

      <section class="editor__controls">
        <!-- Search -->
        <label for="search">Search</label>
        <input 
          v-model="searchQuery"
          type="search" 
          name="search"   
          id="search"
        >

        <span class="editor__separator"></span>
        <!-- Filter -->
        <label for="filter">Filter</label>
        <select
          v-model="selectedFilter"
          name="filter" 
          id="filter"
        >
          <option 
            v-for="filter in filters"
            :key="filter.label"
            :value="filter.value"
          >
            {{ filter.label }}
          </option>
        </select>
      </section>

      <section class="editor__list">
        <ul>
          <li
            v-for="(item, idi) in filteredAvailable"
            :key="`${idi}key`"
          >
            <input 
              v-model="selectedRef"
              :value="item"
              :disabled="isSelectionDisabled(item)"
              :id="`item${idi}`"
              type="checkbox" 
            />
            <label :for="`item${idi}`">{{ item }}</label>
          </li>
          <li v-if="filteredAvailable.length === 0">
            No items or matches
          </li>
        </ul>
      </section>

      <section class="editor__selected">
        <h3>Current selected items:</h3>
        <div class="chips">
          <Chip
            v-for="(item, idi) in selectedRef"
            :key="idi"
            :title="item"
            @remove="removeSelection(item)"
          />
          <div v-if="selectedRef.length === 0">None</div>
        </div>
      </section>

      <hr>

      <footer class="editor__footer">
        <button
          type="button"
          class="text-button save-button"
          @click="save"
          @keypress.enter="save"
        >
          Save
        </button>
        <button 
          type="button"
          class="text-button cancel-button"
          data-a11y-dialog-hide
          @click="emit('close')"
          @keypress.enter="emit('close')"
        >
          Cancel
        </button>
      </footer>

    </div>
  </div>
</template>

<script setup>
import { 
  ref, 
  shallowRef,
  watch, 
  computed,
  onMounted
} from 'vue'
import A11yDialog from 'a11y-dialog'

import Chip from './Chip.vue'

const props = defineProps({
  available: {
    type: Array,
    default: new Array()
  },
  selected: {
    type: Array,
    default: new Array()
  }
})

const emit = defineEmits(['close', 'save'])

// DATA
const nodeRef = ref()
const selectedRef = ref([])
const filters = ref([
  {
    value: -1,
    label: "None"
  },
  {
    value: 10,
    label: "> 10"
  },
  {
    value: 50,
    label: "> 50"
  },
  {
    value: 100,
    label: "> 100"
  }
])
const selectedFilter = ref(filters.value[0].value)
const searchQuery = shallowRef("")

// COMPUTED
const filteredAvailable = computed(() => {
  let result = props.available

  if (!!searchQuery.value) {
    result = result.filter(item => {
      return item.includes(searchQuery.value)
    })
  }
  if (selectedFilter.value >= 0) {
    result = result.filter(item => {
      const itemIndex = Number(item.split(' ')[1])
      return itemIndex > selectedFilter.value
    })
  }

  return result
})

// WATCHES
watch(() => props.selected, (newArr) => {
  selectedRef.value = newArr;
}, 
{ immediate: true })

// METHODS
const removeSelection = (item) => {
  const index = selectedRef.value.indexOf(item)
  if (index >= 0) {
    selectedRef.value.splice(index, 1)
  }

}
const isSelectionDisabled = (item) => {
  const itemIndex = selectedRef.value.indexOf(item)
  if (selectedRef.value.length > 2 && itemIndex < 0) {
    return true
  }
  return false
}
const save = () => {
  emit(
    'save', 
    selectedRef.value, 
    () => { 
      nodeRef.value.hide() 
    }
  )
}

// LIFECYCLE
onMounted(() => {
  const node = document.getElementById('editor')
  nodeRef.value = new A11yDialog(node)
})

</script>

<style scoped>
.editor,
.editor__overlay {
  position: fixed;
  inset: 0;
}
.editor {
  z-index: 2;
  display: flex;
  justify-content: center;
  align-items: center;
}
.editor[aria-hidden='true'] {
  display: none;
}
.editor__container {
  position: relative;
  padding: 1rem;
  max-height: 100vh;
  width: 100%;
  max-width: 65ch;
  margin: 0 auto;
  border: 2px solid var(--color-background-mute);
  border-radius: 0.75rem;
  overflow-y: auto;
  background: var(--color-background);
}
.editor__overlay {
  background-color: var(--vt-c-divider-dark-2);
}
.editor__header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
}
.editor__header .close-button {
  padding: 0.4rem 0.5rem;
  border: none;
  border-radius: 50%;
  font-weight: 700;
  font-size: 1rem;
  line-height: 1rem;
}
.editor__controls {
  display: flex;
}
.editor__controls .editor__separator {
  flex-grow: 1;
}
.editor__controls input, 
.editor__controls select {
  margin-left: 0.5rem;
  width: 25ch;
  padding: 0 0.5rem;
}
.editor__list {
  margin: 1rem 0;
  padding: 1rem;
  background-color: var(--color-background-mute);
  border-radius: 0.75rem;
}
.editor__list ul {
  list-style-type: none;
  padding-left: 0.5rem;
  max-height: 16rem;
  overflow-y: auto;
  scroll-behavior: smooth;
}
.editor__list ul li {
  display: flex;
  align-items: center;
  font-size: 1.1rem;
}
.editor__list ul li + li {
  margin-top: 0.25rem;
}
.editor__list ul li label {
  margin-left: 0.5rem;
}
.chips {
  margin-top: 0.5rem;
}
.editor hr {
  margin: 1rem 0;
  border: none;
  border-top: 2px solid var(--color-background-mute);
}
.save-button {
  background-color: var(--accent);
  color: white;
}
.cancel-button {
  background-color: transparent;
  color: var(--color-text);
  margin-left: 0.25rem;
}
.editor__footer button {
  font-size: 1.2rem;
}
</style>