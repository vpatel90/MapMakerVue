<template>
  <span
    v-if="tile !== null"
    @mousedown="handleClick()"
    :style="{ width: width, height: height }"
    class="square"
    :class="tile.type"
  >
    {{ displayText }}
  </span>
</template>

<script>
export default {
  name: "Tile",
  props: {
    tileInput: Object,
    width: String,
    height: String,
  },
  data() {
    return {
      tileSelections: ["base", "green"],
      tile: null,
    };
  },
  mounted() {
    this.tile = this.tileInput;
  },
  computed: {
    displayText() {
      return `${this.tile.elevation}${this.tile.blocked ? "B" : ""}${
        this.tile.attackerStartingTile ? "|A" : ""
      }${this.tile.defenderStartingTile ? "|D" : ""}`;
    },
  },
  methods: {
    handleClick() {
      if (window.painter !== undefined && window.painter !== "") {
        this.tile.type = window.painter;
      }
      if (window.elevation !== undefined) {
        this.tile.elevation = window.elevation;
      }
      if (window.blocked !== undefined) {
        this.tile.blocked = window.blocked;
      }
      if (window.startingTile === "Attacker") {
        this.tile.defenderStartingTile = false;
        this.tile.attackerStartingTile = true;
      }
      if (window.startingTile === "Defender") {
        this.tile.attackerStartingTile = false;
        this.tile.defenderStartingTile = true;
      }
      if (window.startingTile === "None") {
        this.tile.attackerStartingTile = false;
        this.tile.defenderStartingTile = false;
      }
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.square {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  border: solid 1px;
  color: white;
  user-select: none;
}
.square:hover {
  filter: brightness(150%);
}
.base {
  background-color: #583e23;
}
.green {
  background-color: #88ab75;
}
</style>
