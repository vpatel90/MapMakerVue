<template>
  <div class="main">
    <div class="left-quarter">
      <label for="inputMap">Input Map</label>
      <br />
      <textarea name="inputMap" v-model="inputMap" />
      <br />
      <button @click="generateInputMap()">GenerateInputMap</button>
      <br />
      <label for="name">Name</label>
      <br />
      <input name="name" v-model="name" />
      <br />
      <label for="rowCount">Rows</label>
      <br />
      <input name="rowCount" v-model="rowCount" />
      <br />
      <label for="colCount">Cols</label>
      <br />
      <input name="colCount" v-model="colCount" />
      <br />
      <button @click="generateMap()">GenerateFreshMap</button>
      <br />
      <button @click="logMap()">LogMap</button>
      <br />
      <label for="outputMap">Output Map</label>
      <br />
      <textarea id="outputMap" name="outputMap" v-model="outputMap" />
      <br />
      <button @click="copyToClipboard()">Copy To Clipboard</button>
      <br />
      <button @click="save()">Save (local storage)</button>
      <br />
      <div>Saved Maps</div>
      <button
        v-for="map in savedMaps"
        :key="savedMaps.indexOf(map)"
        class="block"
        @click="setMap(map)"
      >
        {{ getName(map) }}
      </button>
    </div>
    <div class="right-three-quarter">
      <h4>Tile Options</h4>
      <br />
      <div>Types</div>
      <span
        v-for="tile in tileSelections"
        :key="tile"
        class="square type-selection"
        :class="tile"
        @click="setPainter(tile, $event)"
      ></span>
      <br />
      <div>Elevation</div>
      <span
        v-for="n in elevations"
        :key="n"
        class="square elevation-selection"
        @click="setElevation(n, $event)"
      >
        {{ n }}
      </span>
      <br />
      <input type="checkbox" id="checkbox" @change="setBlocked($event)" />
      <label for="checkbox">Block</label>
      <h1>Map</h1>
      <Row
        v-for="row in rows"
        :key="rows.indexOf(row)"
        :tiles="row.tiles"
      ></Row>
      <br />
    </div>
  </div>
</template>

<script>
import Row from "./Row.vue";

export default {
  name: "Map",
  components: {
    Row,
  },
  props: {
    msg: String,
  },
  data() {
    return {
      inputMap: "",
      outputMap: "",
      name: "",
      rowCount: 13,
      colCount: 13,
      tileSelections: ["", "base", "green"],
      elevations: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9],
      rows: [],
      splitter: "|||||",
      savedMaps: [],
    };
  },
  mounted() {
    let maps = window.localStorage.getItem("maps");
    if (maps === null) {
      return;
    }
    this.savedMaps = maps.split(this.splitter);
  },
  computed: {},
  methods: {
    getName(map) {
      const json = JSON.parse(map);
      return json.name;
    },
    createRow(n) {
      const tiles = [];
      for (let i = 0; i < n; i++) {
        tiles.push({
          type: "green",
          elevation: 0,
          blocked: false,
          attackerStartingTile: false,
          defenderStartingTile: false,
        });
      }
      return tiles;
    },
    setMap(map) {
      const json = JSON.parse(map);
      this.name = json.name;
      this.rowCount = json.rowCount;
      this.colCount = json.colCount;
      this.rows = json.rows;
    },
    generateInputMap() {
      if (this.inputMap === "") {
        return;
      }
      this.setMap(this.inputMap);
    },
    generateMap() {
      this.nameMap();
      this.rowCount = parseInt(this.rowCount, 10);
      this.colCount = parseInt(this.colCount, 10);
      this.rows = [];
      for (let i = 0; i < this.rowCount; i++) {
        this.rows.push({ tiles: this.createRow(this.colCount) });
      }
    },
    nameMap() {
      if (this.name === "" || this.name === "Map Name") {
        fetch("https://random-word-api.herokuapp.com/word?number=2")
          .then((response) => response.json())
          .then((data) => {
            this.name = `${data[0]}-${data[1]}`;
          });
      }
    },
    logMap() {
      const map = {
        name: this.name,
        rowCount: this.rowCount,
        colCount: this.colCount,
        rows: this.rows,
      };
      this.outputMap = JSON.stringify(map);
    },
    copyToClipboard() {
      const copyText = document.getElementById("outputMap");
      copyText.select();
      document.execCommand("copy");
    },
    save() {
      let index = -1;
      this.savedMaps.forEach((map, i) => {
        if (JSON.parse(map).name === JSON.parse(this.outputMap).name) {
          index = i;
        }
      });
      if (index !== -1) {
        this.savedMaps[index] = this.outputMap;
      } else {
        this.savedMaps.push(this.outputMap);
      }
      let maps = window.localStorage.getItem("maps");
      if (maps === null) {
        maps = this.outputMap;
      } else {
        maps = this.savedMaps.join(this.splitter);
      }
      window.localStorage.setItem("maps", maps);
    },
    setPainter(t, event) {
      document.getElementsByClassName("type-selection").forEach((el) => {
        el.classList.remove("selected");
      });
      event.target.classList.add("selected");
      window.painter = t;
    },
    setElevation(n, event) {
      document.getElementsByClassName("elevation-selection").forEach((el) => {
        el.classList.remove("selected");
      });
      event.target.classList.add("selected");
      window.elevation = n;
    },
    setBlocked(event) {
      window.blocked = event.target.checked;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
button {
  margin-top: 1rem;
  margin-bottom: 2rem;
}
input {
  margin-bottom: 1rem;
}
.main {
  display: flex;
}
.left-quarter {
  border-right: solid 1px black;
  height: 100%;
  padding-top: 3rem;
}
.right-three-quarter {
  margin: 0 auto;
}
.block {
  display: block;
}
.square {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  border: solid 1px;
  padding: 1rem;
  border: solid 1px black;
  width: 5px;
  height: 5px;
}
.selected {
  border: solid 3px rgb(235, 17, 188);
}
.base {
  background-color: #583e23;
}
.green {
  background-color: #88ab75;
}
</style>
