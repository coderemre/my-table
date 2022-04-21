<template>
  <div
    class="context_menu"
    :style="{
      top: props.contextMenuPos.y + 'px',
      left:
        props.contextMenuPos.x < 220
          ? 220 + 'px'
          : props.contextMenuPos.x + 'px'
    }"
  >
    <div v-for="(item, index) in state.editable" :key="index">
      <label class="m-0">{{ item.label }}</label>
      <div class="grid col-2 m-top-10 m-bottom-10 gap-5">
        <button
          class="btn btn-medium success m-0"
          @click="$emit('editTable', item.name, 'add')"
        >
          Ekle
        </button>
        <button
          class="btn btn-medium danger m-0"
          @click="$emit('editTable', item.name, 'remove')"
        >
          Sil
        </button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { reactive } from 'vue'

interface State {
  editable: { name: string; label: string }[]
}

const props = defineProps({
  contextMenuPos: { type: Object }
})

const state: State = reactive({
  editable: [
    { name: 'row', label: 'Satır' },
    { name: 'column', label: 'Sütun' }
  ]
})
</script>

<style lang="scss">
.context_menu {
  position: absolute;
  display: flex;
  flex-direction: column;
  width: 200px;
  padding: 15px;
  transform: translateX(-100%);
  background-color: white;
  border-radius: 8px;
  label {
    color: var(--primaryColor);
  }
}
</style>
