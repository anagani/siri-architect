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
            v-for="(_cell, cellIndex) in row"
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
              class="absolute inset-0 transition-all duration-100 will-change-[background-color]"
              :style="{
                backgroundColor: getCellColor(rowIndex, cellIndex) || 'transparent',
                opacity: getCellColor(rowIndex, cellIndex) ? 0.8 : 0,
              }"
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
import { ref, onMounted, onUnmounted, watch, computed } from "vue";

interface Props {
  class?: string;
  squareColor: string;
  base?: number;
}

const props = withDefaults(defineProps<Props>(), {
  base: 10,
});

const theme = getColors(props.squareColor);

// Tetris piece shapes (tetrominoes)
const PIECES = [
  [[1, 1], [1, 1]], // O-piece (square)
  [[1, 1, 1, 1]], // I-piece (line)
  [[1, 1, 1], [0, 1, 0]], // T-piece
  [[1, 1, 0], [0, 1, 1]], // S-piece
  [[0, 1, 1], [1, 1, 0]], // Z-piece
  [[1, 1, 1], [1, 0, 0]], // L-piece
  [[1, 1, 1], [0, 0, 1]], // J-piece
];

interface Piece {
  shape: number[][];
  x: number;
  y: number;
  color: string;
  id: number;
}

const el = ref(null);
const grid = ref<(string | null)[][]>([]);
const rows = ref(0);
const cols = ref(0);
const isMobile = ref(false);
const currentPieces = ref<Piece[]>([]);
let pieceIdCounter = 0;

const { width, height } = useElementSize(el);

const effectiveBase = computed(() => {
  return isMobile.value ? props.base / 4 : props.base;
});

function createGrid() {
  grid.value = [];

  for (let i = 0; i < rows.value; i++) {
    grid.value.push(new Array(cols.value).fill(null));
  }
}

function getRandomColor(): string {
  const colors = ['#FFB3BA', '#FFCCCB', '#FFFFBA', '#BAE1BA', '#BAC7FF', '#E0BBE4', '#FFDFD3', '#C7CEEA'];
  return colors[Math.floor(Math.random() * colors.length)] ?? '#ffffff';
}

function getRandomPiece(): Piece {
  const shapeTemplate = PIECES[Math.floor(Math.random() * PIECES.length)];
  if (!shapeTemplate) {
    return {
      shape: [[1, 1], [1, 1]],
      x: Math.floor(cols.value / 2) - 1,
      y: 0,
      color: getRandomColor(),
      id: pieceIdCounter++,
    };
  }
  return {
    shape: shapeTemplate.map(row => [...row]),
    x: Math.floor(Math.random() * (cols.value - 2)),
    y: 0,
    color: getRandomColor(),
    id: pieceIdCounter++,
  };
}

function canPlacePiece(piece: Piece, x: number, y: number): boolean {
  for (let row = 0; row < piece.shape.length; row++) {
    const shapeRow = piece.shape[row];
    if (!shapeRow) continue;
    for (let col = 0; col < shapeRow.length; col++) {
      if (shapeRow[col]) {
        const newX = x + col;
        const newY = y + row;

        if (newX < 0 || newX >= cols.value || newY >= rows.value) {
          return false;
        }

        if (newY >= 0 && grid.value[newY]?.[newX]) {
          return false;
        }
      }
    }
  }
  return true;
}

function lockPiece(piece: Piece) {
  for (let row = 0; row < piece.shape.length; row++) {
    const shapeRow = piece.shape[row];
    if (!shapeRow) continue;
    for (let col = 0; col < shapeRow.length; col++) {
      if (shapeRow[col]) {
        const x = piece.x + col;
        const y = piece.y + row;

        const gridRow = grid.value[y];
        if (y >= 0 && y < rows.value && x >= 0 && x < cols.value && gridRow) {
          gridRow[x] = piece.color;
        }
      }
    }
  }
}

function clearLines() {
  for (let row = rows.value - 1; row >= 0; row--) {
    if (grid.value[row]?.every(cell => cell !== null)) {
      grid.value.splice(row, 1);
      grid.value.unshift(new Array(cols.value).fill(null));
    }
  }
}

function movePieceDown() {
  // Add new pieces randomly
  if (Math.random() < 0.3) {
    currentPieces.value.push(getRandomPiece());
  }

  // Move all pieces down
  for (let i = currentPieces.value.length - 1; i >= 0; i--) {
    const piece = currentPieces.value[i];
    if (!piece) continue;

    if (canPlacePiece(piece, piece.x, piece.y + 1)) {
      piece.y++;
    } else {
      lockPiece(piece);
      clearLines();
      currentPieces.value.splice(i, 1);
    }
  }
}

function getPieceBlocks(): Array<[number, number]> {
  const blocks: Array<[number, number]> = [];
  
  for (const piece of currentPieces.value) {
    for (let row = 0; row < piece.shape.length; row++) {
      const shapeRow = piece.shape[row];
      if (!shapeRow) continue;
      for (let col = 0; col < shapeRow.length; col++) {
        if (shapeRow[col]) {
          blocks.push([piece.y + row, piece.x + col]);
        }
      }
    }
  }
  
  return blocks;
}

function getCellColor(row: number, col: number): string | null {
  const pieceBlocks = getPieceBlocks();
  const inPiece = pieceBlocks.some(([r, c]) => r === row && c === col);

  if (inPiece) {
    // Find which piece this block belongs to and return its color
    for (const piece of currentPieces.value) {
      for (let r = 0; r < piece.shape.length; r++) {
        const shapeRow = piece.shape[r];
        if (!shapeRow) continue;
        for (let c = 0; c < shapeRow.length; c++) {
          if (shapeRow[c] && piece.y + r === row && piece.x + c === col) {
            return piece.color;
          }
        }
      }
    }
  }

  return grid.value[row]?.[col] || null;
}

function calcGrid() {
  isMobile.value = window.innerWidth < 768;

  const cell = width.value / effectiveBase.value;

  rows.value = Math.floor(height.value / cell);
  cols.value = Math.floor(width.value / cell);

  createGrid();
}

watch(width, calcGrid);

let intervalId: ReturnType<typeof setInterval> | undefined;
let timeoutId: ReturnType<typeof setTimeout> | undefined;

onMounted(() => {
  timeoutId = setTimeout(calcGrid, 50);

  window.addEventListener("resize", calcGrid);

  intervalId = setInterval(() => {
    movePieceDown();
  }, 800);
});

onUnmounted(() => {
  clearInterval(intervalId);
  clearTimeout(timeoutId);
  window.removeEventListener("resize", calcGrid);
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
