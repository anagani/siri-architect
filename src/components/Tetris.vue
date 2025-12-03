<template>
  <Transition
    appear
    name="fade"
  >
    <div
      :style="{
        '--cell-size': `${width / cols}px`,
        '--grid-rows': rows - 1,
      }"
      :class="cn('relative w-full', props.class)"
    >
      <div
        ref="el"
        class="absolute inset-0 grid justify-center"
        :style="{ gridTemplateRows: `repeat(var(--grid-rows), var(--cell-size))` }"
      >
        <div
          v-for="(row, rowIndex) in grid"
          :key="rowIndex"
          class="grid flex-1 grid-flow-col"
          :style="{ gridTemplateColumns: `repeat(${cols}, var(--cell-size))` }"
        >
          <div
            v-for="(cell, cellIndex) in row"
            :key="cellIndex"
            :style="{
              '--border-light': theme[100],
              '--border-dark': theme[900],
              '--square-light': theme[500],
              '--square-hover-light': theme[400],
              '--square-dark': theme[700],
              '--square-hover-dark': theme[600],
            }"
            class="relative border border-[color:var(--border-dark)]/40 dark:border-[color:var(--border-dark)]/60"
          >
            <div
              class="absolute inset-0 bg-[color:var(--square-light)] opacity-0 transition-opacity duration-1000 will-change-[opacity] hover:bg-[color:var(--square-hover-light)] dark:bg-[color:var(--square-dark)] dark:hover:bg-[color:var(--square-hover-dark)]"
              :class="[cell && 'cursor-pointer opacity-60']"
              @click="cell && removeCell(rowIndex, cellIndex)"
            />
          </div>
        </div>
      </div>
    </div>
  </Transition>
</template>

<script setup lang="ts">
import { useElementSize } from "@vueuse/core";
import { cn } from "@/lib/utils";
import { getColors } from "theme-colors";
import { ref, onMounted, onUnmounted, watch } from "vue";

interface Props {
  class?: string;
  squareColor: string;
  base?: number;
}

const props = withDefaults(defineProps<Props>(), {
  base: 10,
});

const theme = getColors(props.squareColor);

const el = ref(null);
const grid = ref<(boolean | null)[][]>([]);
const rows = ref(0);
const cols = ref(0);

const { width, height } = useElementSize(el);

function createGrid() {
  grid.value = [];

  for (let i = 0; i < rows.value; i++) {
    grid.value.push(new Array(cols.value).fill(null));
  }
}

function createNewCell() {
  const x = Math.floor(Math.random() * cols.value);
  const row = grid.value[0];
  if (row) {
    row[x] = true;
  }
}

function moveCellsDown() {
  for (let row = rows.value - 1; row >= 0; row--) {
    for (let col = 0; col < cols.value; col++) {
      const currentRow = grid.value[row];
      const nextRow = grid.value[row + 1];
      if (currentRow && currentRow[col] !== null) {
        if (row === rows.value - 1) {
          continue;
        }

        if (nextRow && nextRow[col] === null) {
          nextRow[col] = currentRow[col] ?? null;
          currentRow[col] = null;
        }
      }
    }
  }
}

function clearColumn() {
  for (let col = 0; col < cols.value; col++) {
    const lastRow = grid.value[rows.value - 1];
    if (lastRow && lastRow[col] !== null) {
      lastRow[col] = null;
    }
  }
}

function removeCell(row: number, col: number) {
  const gridRow = grid.value[row];
  if (gridRow) {
    gridRow[col] = null;
  }
}

function calcGrid() {
  const cell = width.value / props.base;

  rows.value = Math.floor(height.value / cell);
  cols.value = Math.floor(width.value / cell);

  createGrid();
}

watch(width, calcGrid);

let intervalId: ReturnType<typeof setInterval> | undefined;
let timeoutId: ReturnType<typeof setTimeout> | undefined;

onMounted(() => {
  timeoutId = setTimeout(calcGrid, 50);

  intervalId = setInterval(() => {
    clearColumn();
    moveCellsDown();
    createNewCell();
  }, 1000);
});

onUnmounted(() => {
  clearInterval(intervalId);
  clearTimeout(timeoutId);
});
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
