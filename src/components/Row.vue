<template>
  <div>
    <Tile
      v-for="tile in tiles"
      :key="tiles.indexOf(tile)"
      :tileInput="tile"
      :width="width"
      :height="height"
    >
    </Tile>
  </div>
</template>

<script>
import Tile from "./Tile.vue";
export default {
  name: "Row",
  components: {
    Tile,
  },
  props: {
    tiles: Array,
  },
  data() {
    return {
      tileSelections: ["base", "green"],
    };
  },
  computed: {
    width() {
      const totalCells = this.tiles.length;
      return `${parseInt(600 / totalCells, 10)}px`;
    },
    height() {
      const totalCells = this.tiles.length;
      return `${parseInt(600 / totalCells, 10)}px`;
    },
  },
  methods: {},
};
</script>

<style scoped>
</style>
