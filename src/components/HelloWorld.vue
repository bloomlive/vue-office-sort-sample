<script>
var requestData = [
  {
    id: 450,
    title: {
      rendered: "Imekontor"
    },
    acf: {
      floor: "3",
      business: ["business"],
      status: {
        value: "vaba",
        label: "Määramata"
      },
      suund: "",
      area: {
        value: "full",
        label: "Terve korrus"
      },
      suurus: "350",
      tookohad: "200"
    }
  },
  {
    id: 453,
    title: {
      rendered: "Koerakontor"
    },
    acf: {
      floor: "6",
      business: ["business"],
      status: {
        value: "vaba",
        label: "Määramata"
      },
      suund: "",
      area: {
        value: "full",
        label: "Terve korrus"
      },
      suurus: "400",
      tookohad: "100"
    }
  },
  {
    id: 451,
    title: {
      rendered: "Kassikontor"
    },
    acf: {
      floor: "1",
      business: ["business"],
      status: {
        value: "vaba",
        label: "Määramata"
      },
      suund: "",
      area: {
        value: "full",
        label: "Terve korrus"
      },
      suurus: "250",
      tookohad: "200"
    }
  },
  {
    id: 452,
    title: {
      rendered: "Põrsakontor"
    },
    acf: {
      floor: "6",
      business: ["business"],
      status: {
        value: "broneeritud",
        label: "Määramata"
      },
      suund: "",
      area: {
        value: "full",
        label: "Terve korrus"
      },
      suurus: "400",
      tookohad: "123"
    }
  }
];

export default {
  components: [],
  props: {},
  methods: {
    getFilteredOffices() {
      const areaMin = this.sliderAreaMin;
      const areaMax = this.sliderAreaMax;
      const jobsMin = this.sliderJobMin;
      const jobsMax = this.sliderJobMax;
      const floors = this.selectedFloors;

      return this.offices.filter(function (item) {
        if (item.acf.suurus < areaMin || item.acf.suurus > areaMax) {
          return false;
        }

        if (item.acf.tookohad < jobsMin || item.acf.tookohad > jobsMax) {
          return false;
        }

        if (!floors.includes(item.acf.floor)) {
          return false;
        }

        return true;
      });
    }
  },
  computed: {
    getFilteredAndSortedOffices() {
      const list = [];
      const source = this.getFilteredOffices();

      source.forEach(office => {
        if (list[office.acf.floor]) {
          list[office.acf.floor].push(office);
        } else {
          list[office.acf.floor] = [office];
        }
      })

      console.log(list.filter(n => n));

      return list.filter(n => n);
    },
    getAvailableFloors() {
      const set = new Set();

      const sorted = this.offices.sort((a, b) => {
        if (a.acf.floor > b.acf.floor) {
          return 1;
        }
        if (a.acf.floor < b.acf.floor) {
          return -1;
        }
        return 0;
      });

      sorted.forEach((office) => {
        set.add(office.acf.floor);
      });

      return set;
    },
    sliderJobMin: {
      get: function () {
        return parseInt(this.minJobAngle);
      },
      set: function (val) {
        val = parseInt(val);
        if (val > this.maxJobAngle) {
          this.maxJobAngle = val;
        }
        this.minJobAngle = val;
      }
    },
    sliderJobMax: {
      get: function () {
        return parseInt(this.maxJobAngle);
      },
      set: function (val) {
        val = parseInt(val);
        if (val < this.minJobAngle) {
          this.minJobAngle = val;
        }
        this.maxJobAngle = val;
      }
    },
    sliderAreaMin: {
      get: function () {
        return parseInt(this.minAreaAngle);
      },
      set: function (val) {
        val = parseInt(val);
        if (val > this.maxAreaAngle) {
          this.maxAreaAngle = val;
        }
        this.minAreaAngle = val;
      }
    },
    sliderAreaMax: {
      get: function () {
        return parseInt(this.maxAreaAngle);
      },
      set: function (val) {
        val = parseInt(val);
        if (val < this.minAreaAngle) {
          this.minAreaAngle = val;
        }
        this.maxAreaAngle = val;
      }
    }
  },
  data() {
    return {
      offices: requestData,
      selectedFloors: ["1", "6"],
      minJobAngle: 40,
      maxJobAngle: 300,
      minAreaAngle: 200,
      maxAreaAngle: 1000
    }
  }
};


</script>

<template>
  <div id="app">
    <fieldset>
      <label for="">Kontori suurus m<sup>2</sup></label>
      <div class="range-slider Area-slider">
        {{ sliderAreaMin }}
        <input type="range" min="200" max="1000" step="100" v-model="sliderAreaMin">
        <input type="range" min="200" max="1000" step="100" v-model="sliderAreaMax">
        {{ sliderAreaMax }}
      </div>

    </fieldset>
    <fieldset>
      <label for="">Töökohtade arv</label>
      <div class="range-slider job-slider">
        {{ sliderJobMin }}
        <input type="range" min="40" max="300" step="10" v-model="sliderJobMin">

        <input type="range" min="40" max="300" step="10" v-model="sliderJobMax">
        {{ sliderJobMax }}
      </div>
    </fieldset>
    <fieldset>
      <label v-for="item in this.getAvailableFloors">
        <input type="checkbox" name="floor" :value="item" v-model="selectedFloors"> {{ item }}
      </label>
      Valitud korrused: {{ selectedFloors }}
    </fieldset>
    <button>Näita kõiki</button>

    <!-- sorteeri -->
    <p>Sorteeri ASC ja DESC</p>
    <button>Korrus // floor</button>
    <button>Töökohad // tookohad</button>
    <button>Suurus // suurus</button>
    <hr>
    <!-- list -->
    <ul v-for="floor in this.getFilteredAndSortedOffices">
      <li>Korrus: {{floor[0].acf.floor}}
        <ul>
          <li v-for="office in floor">
            {{ office.title.rendered }}, {{ office.acf.status.value }}, {{ office.acf.tookohad }} tk, {{ office.acf.suurus }} m<sup>2</sup>
          </li>
        </ul>
      </li>
    </ul>
  </div>
</template>

<style scoped>
/* range slider */
.range-slider {
  width: 300px;
  text-align: center;
  position: relative;
  /*   height: 6em;*/
}

.range-slider input[type="range"] {
  position: absolute;
  left: 0;
  bottom: 0;
}

/*
input[type=number] {
  border: 1px solid #ddd;
  text-align: center;
  font-size: 1.6em;
  -moz-appearance: textfield;
}
*/
input[type="number"]::-webkit-outer-spin-button,
input[type="number"]::-webkit-inner-spin-button {
  -webkit-appearance: none;
}

input[type="number"]:invalid,
input[type="number"]:out-of-range {
  border: 1px solid #ff6347;
}

input[type="range"] {
  -webkit-appearance: none;
  width: 100%;
}

input[type="range"]:focus {
  outline: none;
}

input[type="range"]:focus::-webkit-slider-runnable-track {
  background: #2497e3;
}

input[type="range"]:focus::-ms-fill-lower {
  background: #2497e3;
}

input[type="range"]:focus::-ms-fill-upper {
  background: #2497e3;
}

input[type="range"]::-webkit-slider-runnable-track {
  width: 100%;
  height: 1px;
  cursor: pointer;
  animate: 0.2s;
  background: #2497e3;
  border-radius: 1px;
  box-shadow: none;
  border: 0;
}

input[type="range"]::-webkit-slider-thumb {
  z-index: 2;
  position: relative;
  box-shadow: 0px 0px 0px #000;
  height: 10px;
  width: 10px;
  border-radius: 100%;
  background: #ccc;
  cursor: pointer;
  -webkit-appearance: none;
  margin-top: -3px;
}

</style>
