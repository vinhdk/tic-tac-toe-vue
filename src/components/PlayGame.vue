<script lang="ts">
import { ref } from "vue";
export interface IItem {
  id: number;
  player: "o" | "x" | undefined;
}

export interface IData {
  items: IItem[];
  currentPlayer: "o" | "x" | undefined;
  supporter: { [key: number]: number };
  winner: string | undefined;
}

export interface IMethod extends IData {
  click: (item: IItem) => void;
  check: () => void;
  reset: () => void;
  initItem: () => void;
}

export default {
  name: "PlayGame",
  setup() {
    let items = ref(Array.from({length: 12}).map((e, i) => ({
      id: i + 1,
      player: undefined
    })) as IItem[]);
    let currentPlayer: "o" | "x" | undefined = "o";
    let supporter: { [key: number]: number } = {
      1: 0,
      2: 1,
      3: 2,
      4: 0,
      5: 1,
      6: 2,
      7: 0,
      8: 1,
      9: 2,
      10: 0,
      11: 1,
      12: 2
    };
    let winner: string | undefined = undefined;
    function click(item: IItem): void {
      if (item.player) {
        return;
      }
      item.player = currentPlayer;

      setTimeout(() => {
        check();

        if (winner) {
          return;
        }
        currentPlayer = currentPlayer === "o" ? "x" : "o";
      });
    }
    function check(): void {
      const ownItems = items.value.filter((e) => e.player === currentPlayer);
      if (ownItems.length < 3) {
        return;
      }
      const conditions: number[][] = [[], [], []];
      ownItems.forEach((e: IItem) => {
        const position: number = supporter[e.id];
        conditions[position].push(e.id);
      });
      const check = conditions.some((e) => e.length === 4);
      if (!check) {
        const filter = items.value.filter((e) => !!e.player);
        if (filter.length === 12) {
          alert("Tie");
          initItem();
        }
        return;
      }
      alert("Winner is " + currentPlayer);
      initItem();
      reset();
    }
    function reset(): void {
      currentPlayer = winner === "o" ? "x" : "o";
      winner = undefined;
    }
    function initItem(): void {
      items.value = Array.from({length: 12}).map((e, i) => ({
        id: i + 1,
        player: undefined
      })) as IItem[];
    }

    return {items, click};
  },
};
</script>

<template>
  <div class="root">
    <div class="container">
      <div
          class="item"
          v-for="item in items"
          :key="item.id"
          @click="click(item)"
      >
        <span
            v-if="item.player"
            :class="(item.player ? item.player : '')"
        >
          {{ item.player }}
        </span>
      </div>
    </div>
  </div>
</template>

<style>
.root {
  width: 100vw;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}

.container {
  display: flex;
  flex-flow: wrap;
  width: 304px;
  border: 2px solid gray;
}

.item {
  width: 100px;
  height: 100px;
  display: flex;
  justify-content: center;
  align-items: center;
  border: 2px solid gray;
}

.item > span {
  font-size: 48px;
  line-height: 72px;
  text-transform: uppercase;
}

.item > span.x {
  color: red;
}

.item > span.o {
  color: blue;
}
</style>
