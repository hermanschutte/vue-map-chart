<template>
  <div class="vue-world-map">
    <Map @hoverCountry="onHoverCountry" @hoverLeaveCountry="onHoverLeaveCountry" />
    <div
      v-show="legend.name && countryData[legend.code] > 0"
      class="vue-map-legend font-sans rounded-lg bg-gray-900 border-none px-4 py-3"
      :style="'left:' + position.left + 'px; top: ' + position.top + 'px'"
    >
      <div class="vue-map-legend-header bg-gray-900 text-white text-sm font-medium text-center border-none rounded-t-lg">
        <span>{{legend.name}}</span>
      </div>
      <div class="vue-map-legend-content mt-1 bg-gray-900 text-white font-bold text-sm border-none rounded-b-lg">
        <span>Visitors: {{countryData[legend.code] || 0}}</span>
      </div>
    </div>
  </div>
</template>

<script>
import chroma from "chroma-js";
import Map from "./Map";
import {
  getDynamicMapCss,
  getBaseCss,
  getCombinedCssString
} from "./dynamic-map-css";

let legend = {
  data: null,
  code: null,
  name: null
};

let position = {
  left: 0,
  top: 0
};

export default {
  name: "MapChart",
  components: { Map },
  watch: {
    countryData() {
      this.renderMapCSS();
    }
  },
  props: {
    lowColor: {
      type: String,
      default: "#fde2e2"
    },
    highColor: {
      type: String,
      default: "#d83737"
    },
    chromaScaleOn: {
      type: Boolean,
      default: true
    },
    countryData: {
      type: Object,
      required: true
    },
    defaultCountryFillColor: {
      type: String,
      default: "#dadada"
    },
    countryStrokeColor: {
      type: String,
      default: "#909090"
    },
    legendHeaderBackgroundColor: {
      type: String,
      default: "white"
    },

    legendContentBackgroundColor: {
      type: String,
      default: "#dadbda8f"
    },
    legendFontColorHeader: {
      type: String,
      default: "black"
    },
    legendFontColorContent: {
      type: String,
      default: "black"
    },
    legendBorderColor: {
      type: String,
      default: "gray"
    },
    legendBorderRadius: {
      type: Number,
      default: 5
    },
    legendBoxShadow: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      legend: legend,
      position: position,
      node: document.createElement("style"),
      chromaScale: chroma.scale([this.$props.lowColor, this.$props.highColor])
    };
  },

  methods: {
    onHoverCountry(country) {
      this.legend = country;
      this.position = country.position;
      this.$emit("hoverCountry", country);
    },
    onHoverLeaveCountry(country) {
      this.legend = {
        data: null,
        code: null,
        name: null
      };
      this.$emit("hoverLeaveCountry", country);
    },
    renderMapCSS() {
      const baseCss = getBaseCss(this.$props);
      const dynamicMapCss = getDynamicMapCss(
        this.$props.countryData,
        this.chromaScale,
        this.$props.highColor,
        this.$props.chromaScaleOn
      );
      this.$data.node.innerHTML = getCombinedCssString(baseCss, dynamicMapCss);
    }
  },
  mounted() {
    document.body.appendChild(this.$data.node);
    this.renderMapCSS();
  }
};
</script>

<style scoped>
.vue-world-map,
#map-svg {
  height: 100%;
}

.vue-world-map {
  position: relative;
}

.vue-map-legend {
  overflow: auto;
  position: absolute;
}

.vue-map-legend:after {
    content: "";
    display: inline-block;
    width: 0;
    height: 0;
    border: 5px solid transparent;
    border-top-color: #111827;
    position: absolute;
    bottom: -10px;
    left: 50%;
}
</style>