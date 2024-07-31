<template>
  <section class="widget">
    <header>
      <h1>Select items</h1>
      <p>{{ infoOnSelected }}</p>
    </header>
    
    <main>
      <div 
        v-if="selected.length > 0" 
        class="chips"
      >
        <Chip
          v-for="(item, idi) in selected"
          :key="idi"
          :title="item"
          @remove="removeSelection(item)"
        />
      </div>
      <div v-else>None</div>
      <button 
        type="button"
        class="open-editor-button text-button"
        data-a11y-dialog-show="editor"
        @click="isEditorOpen = true"
        @keypress.enter="isEditorOpen = true"
      >
        Change my choice
      </button>
    </main>

    <Transition name="modal">
      <SelectItemsEditor
        v-show="isEditorOpen"
        :selected
        :available
        @save="updateSelected"
        @close="isEditorOpen = false"
      />
    </Transition>
  </section>
</template>

<script setup>
import { ref, toValue, computed } from 'vue'

import Chip from './Chip.vue'
import SelectItemsEditor from './SelectItemsEditor.vue'

const props = defineProps({
  available: {
    type: Array,
    default: new Array()
  }
})

// DATA
const selected = defineModel('selected', { type: Array });
const isEditorOpen = ref(false)

// COMPUTED
const infoOnSelected = computed(() => {
  const selectedLength = props.selected.length
  return selectedLength === 0 
  ? "No items selected" 
  : selectedLength === 1
  ? "You currently have 1 selected item."
  : selectedLength > 1
  ? `You currently have ${selectedLength} selected items.`
  : "Unexpected number of items"
})

// METHODS
const removeSelection = (item) => {
  const index = selected.value.indexOf(item)
  if (index >= 0) {
    selected.value.splice(index, 1)
  }
}
const updateSelected = (newArray, closeFn) => {
  selected.value = toValue(newArray)
  closeFn()
}
</script>

<style>
.widget main {
  margin: 1rem 0;
}
.widget .open-editor-button {
  margin-top: 1rem;
}
</style>