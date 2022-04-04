<script lang="ts">
export interface IItem {
  id: number;
  player: "o" | "x" | undefined;
}

export default {
  name: "PlayGame",
  data() {
    return {
      items: Array.from({ length: 12 }).map((e, i) => ({
        id: i + 1,
        player: undefined
      })) as IItem[],
      currentPlayer: "o" as "o" | "x" | undefined,
      supporter: {
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
      },
      winner: undefined
    };
  },
  methods: {
    click(item: IItem): void {
      if (item.player) {
        return;
      }
      item.player = this.currentPlayer;

      this.check();

      if (this.winner) {
        return;
      }
      this.currentPlayer = this.currentPlayer === "o" ? "x" : "o";
    },
    check(): void {
      const items = this.items.filter((e) => e.player === this.currentPlayer);
      if (items.length < 3) {
        return;
      }
      const conditions: number[][] = [[], [], []];
      items.forEach((e: IItem) => {
        const position: number = this.supporter[e.id];
        conditions[position].push(e.id);
      });
      const check = conditions.some((e) => e.length === 4);
      if (!check) {
        const filter = this.items.filter((e) => !!e.player);
        if (filter.length === 12) {
          console.log("Tie");
          this.initItem();
        }
        return;
      }
      console.log("Winner is " + this.currentPlayer);
      this.initItem();
      this.reset();
    },
    reset(): void {
      this.currentPlayer = this.winner === "o" ? "x" : "o";
      this.winner = undefined;
    },
    initItem(): void {
      this.items = Array.from({ length: 12 }).map((e, i) => ({
        id: i + 1,
        player: undefined
      })) as IItem[];
    }
  }
};
</script>

<template>
  <div class="root">
    <div class="container">
      <div
        class="item"
        v-for="item in items"
        v-bind:key="item.id"
        v-on:click="click(item)"
      >
        <span
          v-if="item.player"
          v-bind:class="(item.player ? item.player : '')"
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
