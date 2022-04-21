<template>
  <div class="my_table">
    <Transition>
      <table v-if="state?.columns?.length > 0 && state?.rows?.length > 0">
        <tbody>
          <tr
            :id="row_item.toString()"
            v-for="row_item in state.rows"
            :key="row_item"
          >
            <td
              v-for="column_item in state.columns"
              :id="column_item.toString()"
              :key="column_item"
              contenteditable="true"
              @click.right="rightClick"
            ></td>
          </tr>
        </tbody>
      </table>
    </Transition>
    <Transition>
      <div
        v-if="state?.columns?.length <= 0 && state?.rows?.length <= 0"
        class="max w480"
      >
        <div class="grid col-2 gap-5">
          <label class="bold">
            Sütun
            <br />
            <br />
            <input
              type="number"
              :min="0"
              class="input-medium max"
              v-model="state.columnsCount"
            />
          </label>
          <label class="bold">
            Satır
            <br />
            <br />
            <input
              type="number"
              :min="0"
              class="input-medium max"
              v-model="state.rowsCount"
            />
          </label>
        </div>

        <button
          class="btn btn-medium primary max m-top-20"
          @click="createTable"
        >
          Tabloyu Oluştur
        </button>
      </div>
    </Transition>
  </div>
  <ContextMenu
    v-show="state.showContextMenu"
    @editTable="editTable"
    :contextMenuPos="state.contextMenuPos"
  />
</template>

<script setup lang="ts">
import { onMounted, onUnmounted, reactive, ref } from 'vue'
import ContextMenu from '@/components/ContextMenu.vue'

onMounted(() => {
  document.addEventListener('click', () => (state.showContextMenu = false))
})

onUnmounted(() => {
  document.removeEventListener('click', () => (state.showContextMenu = false))
})

interface State {
  rows: number[]
  columns: number[]
  columnsCount: number
  rowsCount: number
  contextMenuPos: object
  showContextMenu: boolean
}

const state: State = reactive({
  rows: [],
  columns: [],
  columnsCount: 0,
  rowsCount: 0,
  contextMenuPos: {},
  showContextMenu: false
})

const clickElement = ref()

const rightClick = (e: MouseEvent) => {
  clickElement.value = e
  state.contextMenuPos = { x: e.clientX, y: e.clientY }
  state.showContextMenu = true
  e.preventDefault()
}

const fillArray = (arr: number[], length: number) => {
  for (let i = 0; i < length; i++) {
    arr.push(i)
  }
  return arr
}

const createTable = () => {
  if (state.rowsCount > 0 && state.columnsCount > 0) {
    state.rows = fillArray(state.rows, state.rowsCount)
    state.columns = fillArray(state.columns, state.columnsCount)
  } else {
    alert('Sütun ve satır adetini giriniz.')
  }
}

const editTable = (element: string, process: string) => {
  let index = element == 'row' ? 1 : 0
  var arr = state[element == 'row' ? 'rows' : 'columns']

  let arrMaxValue = Math.max(...arr)
  let id: number = parseInt(clickElement.value.path[index].id)

  switch (process) {
    case 'add':
      arr.splice(arr.indexOf(id) + 1, 0, arrMaxValue + 1)
      break
    case 'remove':
      arr.splice(arr.indexOf(id), 1)

      break
  }

  state[element == 'row' ? 'rows' : 'columns'] = arr
}
</script>

<style lang="scss">
.my_table {
  top: 200px;
  @media (max-width: 1280px) {
    padding: 0 20px;
  }
  table {
    width: 100%;
    border-collapse: collapse;
    tbody {
      display: table-row-group;
      vertical-align: middle;
      tr {
        display: table-row;
        vertical-align: inherit;
        td {
          padding: 12px;
          border: 1px solid var(--primaryColor);
        }
      }
    }
  }

  .v-enter-active,
  .v-leave-active {
    transition: transform 1s ease-in-out;
  }
  .v-enter-from {
    transform: translateY(1000%);
  }
  .v-enter-to {
    transform: translateY(0%);
  }
  .v-leave-from {
    transform: translateY(0%);
  }
  .v-leave-to {
    transform: translateY(-1000%);
  }
}
</style>
