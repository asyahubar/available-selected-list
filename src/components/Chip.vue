<template>
  <div class="chip">
    <span class="chip__title">{{ title }}</span>

    <button 
      type="button" 
      title="Unselect"
      class="chip__button"
      @click="emit('remove')"
      @keypress.enter="emit('remove')"
    >
      &#10005;
    </button>
  </div>
</template>

<script setup>
const props = defineProps({
  title: {
    type: String,
    default: ""
  }
})

const emit = defineEmits(['remove'])
</script>

<style>
.chips {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
}
.chip {
  display: flex;
  padding: 0.25rem 0.75rem 0.25rem 1rem;
  border-radius: 1rem;
  font-size: 0.85rem;
  background-color: var(--color-background-mute);
  color: var(--color-text);
}
.chip__title {
  min-width: 10ch;
  padding-right: 0.75rem;
  border-right: 1px solid var(--color-border);
  margin-right: 0.5rem;
}
.chip__button {
  padding: 2px 6px;
  background-color: transparent;
  border: none;
  border-radius: 50%;
  color: var(---color-text);
  line-height: 1;
  transition: background-color 0.5s ease;
}
.chip__button:hover {
  background-color: rgba(0, 0, 0, 0.2);
}
@media (prefers-color-scheme: dark) {
  .chip__button:hover {
    background-color: rgba(255, 255, 255, 0.2);
  }
}
</style>