<script setup>
import * as d3 from "d3";
import * as geo from "d3-geo";
import * as d3Color from "d3-scale-chromatic";
import chinaGeoData from "../assets/china.geo.json";
import sexismRatioMap from "../assets/sexism_ratio_by_province.json";
import {
  defineComponent,
  computed,
  onMounted,
  ref,
  getCurrentInstance,
  inject,
} from "vue";
const extraGeo = [
  {
    name: "China",
    svgPath:
      "M892.8 854.4c-3.2 0 0-6.4-6.4-6.4v-3.2h-6.4s0-3.2-3.2-3.2-3.2-3.2-3.2-3.2l-3.2-3.2c0-3.2 3.2-6.4 3.2-12.8 0 0 0-3.2-3.2-3.2 0-6.4 3.2-12.8 6.4-19.2v-9.6l3.2-3.2v-6.4c0-3.2 6.4-3.2 6.4-6.4 3.2 0 3.2 0 6.4-3.2h6.4s3.2 3.2 6.4 3.2c3.2 12.8 0 28.8 0 44.8-3.2 6.4 0 16-6.4 22.4-3.2 3.2 0 6.4 0 12.8 0 3.2 0 3.2-3.2 6.4 0-6.4 0-6.4-3.2-6.4zM646.4 960s-3.2-3.2-6.4-3.2h-6.4l-3.2-3.2v-6.4-9.6s3.2-3.2 3.2-6.4c3.2-3.2 9.6-9.6 16-12.8 3.2 0 6.4 0 9.6-3.2 3.2 0 3.2 3.2 6.4 3.2h3.2v-3.2s-3.2 0-6.4-3.2l-3.2-3.2c0-3.2-3.2-6.4-3.2-9.6l-6.4-3.2v-6.4H643.2l-3.2-3.2c-3.2 0-6.4-3.2-12.8 0-3.2 0-3.2 3.2-6.4 3.2H611.2c-3.2 0-6.4-3.2-12.8-6.4h-9.6l-9.6-9.6s0-3.2-3.2-6.4v-12.8h-3.2-6.4c-6.4-3.2-12.8-6.4-16-9.6 0 3.2-3.2 3.2-6.4 6.4 0 3.2-6.4 3.2-6.4 6.4-3.2 3.2-6.4 0-9.6 0-3.2 0-3.2 3.2-6.4 3.2-6.4-3.2-16 0-22.4-3.2-3.2 0-3.2 0-6.4 3.2v3.2H483.2h-3.2c0 6.4 3.2 16 0 25.6-3.2 0-3.2 3.2-6.4 3.2h-6.4c-3.2 0-3.2-3.2-6.4-6.4 0-3.2 0-3.2-3.2-6.4-3.2 0-3.2 3.2-6.4 3.2h-6.4c0-3.2-3.2-3.2-3.2-3.2s0-3.2-3.2-3.2c0 0-3.2 0-3.2-3.2 0 0-3.2-3.2-6.4-3.2l-3.2-3.2s-3.2-3.2-3.2-6.4v-9.6c3.2 0 0-3.2 3.2-6.4 0-3.2-3.2-3.2-6.4-3.2 0-3.2-3.2-3.2-6.4-6.4v-12.8c-3.2-3.2-12.8 0-19.2 0h-3.2c0-6.4-3.2-12.8 0-22.4 0-3.2 3.2-3.2 6.4-6.4v-3.2c3.2-3.2 3.2-6.4 9.6-9.6 3.2-3.2 3.2-6.4 6.4-6.4V768c0-3.2 3.2-3.2 3.2-6.4v-9.6c3.2-6.4 0-6.4 0-9.6l-3.2-3.2h-3.2c-3.2-6.4-6.4-12.8-6.4-16h-3.2s-3.2 6.4-6.4 6.4c-3.2 3.2-9.6 0-12.8 0-6.4-3.2-9.6-6.4-16-9.6h-6.4l-3.2 3.2c-3.2 0-3.2 3.2-6.4 3.2h-6.4c-3.2 0-3.2 3.2-9.6 6.4-3.2 3.2-9.6 3.2-12.8 6.4-3.2 0-3.2 3.2-6.4 3.2h3.2c-3.2 0-3.2 3.2-6.4 3.2H291.2l-3.2-3.2v-9.6c-3.2-3.2-6.4-6.4-9.6-6.4 0-3.2-3.2-3.2-6.4-6.4v-3.2l-3.2-12.8-3.2-3.2c-3.2-3.2-6.4-3.2-12.8-3.2l-3.2-3.2h-6.4c-3.2 3.2-6.4 3.2-6.4 6.4s-3.2 3.2-3.2 6.4c0 0-3.2 0-6.4 3.2 0 0-3.2 0-6.4-3.2s0-12.8-3.2-16c-3.2 0-3.2 3.2-9.6 3.2-3.2 0-3.2-3.2-6.4-3.2h-3.2l-6.4-3.2c-3.2 0-9.6-3.2-16-6.4-3.2 0-3.2-3.2-6.4-3.2h-12.8c0-3.2-3.2-3.2-3.2-6.4-3.2 0-6.4-3.2-9.6-3.2 0 0 0-3.2-3.2-3.2v-3.2c-3.2 0-3.2 0-6.4-3.2l-3.2-3.2-3.2-3.2V640h-3.2c-3.2 0-6.4 0-9.6-3.2-6.4-12.8-12.8-22.4-25.6-28.8h-6.4c-3.2 0-3.2 3.2-6.4 6.4 0 0-3.2 0-6.4-3.2-3.2-6.4-6.4-9.6-9.6-12.8-3.2-3.2-3.2-6.4-3.2-9.6-3.2-3.2-6.4-6.4-9.6-12.8h-3.2c0-3.2-3.2-6.4-6.4-6.4-3.2-9.6 0-19.2 0-28.8v-6.4c3.2 0 3.2-3.2 6.4-3.2s3.2 3.2 6.4 3.2l3.2-3.2c0-9.6-3.2-16-3.2-25.6 0-3.2 3.2-3.2 3.2-9.6-3.2-9.6-6.4-16-6.4-25.6 0-3.2-3.2 0-6.4-3.2h3.2c-6.4-3.2-9.6-9.6-12.8-12.8-3.2 0-3.2-3.2-6.4-3.2-3.2-6.4 0-16-3.2-25.6-3.2 0-3.2-6.4-6.4-6.4s-6.4-3.2-9.6-6.4v-6.4c3.2-3.2 6.4-3.2 9.6-3.2v-6.4c0-3.2 3.2-3.2 3.2-9.6v-6.4s0-3.2-3.2-6.4-6.4 0-12.8-3.2l-3.2-3.2v-6.4l6.4-6.4v-12.8c3.2 0 3.2 0 3.2-3.2 3.2 0 6.4-3.2 9.6-6.4 3.2-3.2 9.6 0 12.8 0l3.2-3.2c3.2 0 9.6 3.2 12.8 3.2 3.2 0 3.2 3.2 6.4 3.2h9.6c3.2 0 3.2-3.2 9.6-3.2v-3.2s3.2 0 3.2-3.2c3.2 0 9.6 0 9.6-3.2 9.6-3.2 19.2 0 28.8 0 9.6-3.2 12.8-3.2 19.2-3.2h3.2c3.2-3.2 6.4-9.6 6.4-16l3.2-3.2c3.2 0 3.2-3.2 3.2-3.2V262.4c3.2 0 0-3.2 3.2-6.4 3.2 0 3.2-3.2 6.4-3.2 6.4 0 16-3.2 22.4 3.2H195.2v-3.2c0-3.2 0-6.4 3.2-12.8l3.2-3.2v-3.2c0-3.2 3.2-3.2 3.2-3.2 3.2-6.4 6.4-6.4 6.4-9.6 3.2 0 0-3.2 3.2-6.4 3.2 0 3.2-3.2 6.4-3.2 6.4 0 6.4 3.2 12.8 3.2 3.2 0 3.2 3.2 6.4 3.2h6.4c3.2 0 6.4 0 9.6-3.2 3.2 0 0-3.2 3.2-6.4 0-3.2 0-9.6 3.2-16 0 0 3.2 0 3.2-3.2h6.4c3.2 3.2 6.4 3.2 6.4 3.2v-3.2s0-3.2 3.2-3.2c9.6-3.2 19.2-6.4 25.6-3.2 3.2 6.4 0 12.8 3.2 19.2 3.2 0 3.2 3.2 6.4 6.4 3.2 0 3.2 3.2 3.2 6.4 0 0 3.2 3.2 6.4 3.2h3.2c3.2 0 3.2 3.2 6.4 3.2 0 0 0 3.2 3.2 3.2v16h3.2c3.2 6.4 0 12.8 3.2 19.2 0 3.2-3.2 9.6-6.4 12.8v12.8c6.4 3.2 9.6 3.2 16 3.2v3.2c6.4 0 12.8 3.2 19.2 6.4h3.2c3.2 0 6.4 6.4 12.8 6.4v6.4c3.2 0 3.2 6.4 6.4 6.4 0 0 3.2 0 3.2 3.2 3.2 3.2 0 6.4 3.2 12.8l3.2 3.2c0 3.2 3.2 3.2 3.2 6.4 0 0 0 3.2 3.2 3.2v6.4s3.2 0 3.2 3.2c0 0 3.2 3.2 6.4 3.2s3.2 3.2 6.4 3.2c6.4 0 16 0 22.4 3.2l3.2 3.2c12.8 0 22.4 3.2 38.4 3.2 3.2 0 3.2 3.2 6.4 3.2h6.4c3.2 3.2 3.2 3.2 6.4 3.2v3.2c3.2 3.2 9.6 0 12.8 3.2 0 3.2 6.4 3.2 6.4 9.6h19.2c3.2 0 3.2 3.2 6.4 3.2 6.4-3.2 9.6-3.2 16-9.6h6.4V384c3.2 0 6.4 0 9.6-3.2l3.2-3.2c9.6-3.2 16-3.2 25.6-6.4h32c3.2 0 3.2-3.2 3.2-6.4 3.2-3.2 6.4-6.4 9.6-6.4h3.2c3.2 0 3.2-3.2 3.2-6.4 3.2 0 3.2-3.2 3.2-3.2v-3.2l-3.2-3.2c-3.2-3.2-6.4-6.4-6.4-12.8v-12.8c3.2 0 3.2 0 3.2-3.2 3.2 0 3.2-3.2 6.4-3.2 9.6 0 19.2 3.2 28.8 0 3.2 0 3.2 0 6.4-3.2v-3.2h3.2l9.6-9.6c3.2 0 6.4 0 9.6-3.2l3.2-3.2c3.2 0 6.4 0 9.6-3.2 0-3.2 3.2-6.4 3.2-12.8l3.2-3.2h6.4c3.2-3.2 6.4-6.4 12.8-9.6h3.2l3.2-3.2h16l3.2-3.2c0-3.2-3.2-3.2-3.2-6.4-3.2 0-3.2-3.2-3.2-3.2-3.2 0-3.2-3.2-6.4-6.4h-3.2l-3.2-3.2h-9.6l-3.2 3.2c-3.2 0-3.2 3.2-6.4 3.2h-6.4l-3.2-3.2H739.2v12.8c-3.2 0-6.4 3.2-9.6 3.2l-3.2-3.2-3.2-3.2c0-6.4 0-12.8 3.2-19.2h3.2c0-6.4 3.2-9.6 3.2-12.8h3.2c0-6.4 3.2-9.6 6.4-12.8h19.2c3.2 0 3.2 0 3.2-3.2l9.6-9.6c3.2-3.2 0-9.6 3.2-12.8 3.2-3.2 3.2-9.6 6.4-12.8h3.2v-6.4l3.2-3.2 3.2-3.2v-9.6h-9.6l-3.2-3.2v-16c0-3.2 3.2-3.2 3.2-6.4h3.2c3.2 0 3.2 0 3.2-3.2 0 0 0-3.2 3.2-3.2h3.2c3.2 0 6.4-3.2 9.6 0 3.2 0 3.2-3.2 6.4-3.2s3.2-3.2 6.4-3.2h9.6c6.4-6.4 6.4-6.4 12.8-6.4 3.2 0 6.4 3.2 9.6 6.4 3.2 0 3.2 0 3.2-3.2h9.6c0 3.2 3.2 3.2 6.4 3.2 0 0 0 3.2 3.2 3.2 3.2 3.2 3.2 6.4 6.4 9.6 0 3.2 0 6.4 3.2 6.4h3.2s0 6.4 3.2 6.4c3.2 3.2 6.4 3.2 6.4 6.4s3.2 3.2 3.2 6.4h6.4s0 3.2 3.2 3.2c0 3.2 3.2 9.6 3.2 12.8l3.2 3.2 3.2 3.2c6.4 3.2 16 0 22.4 3.2 3.2 3.2 6.4 3.2 9.6 6.4h9.6c0 3.2 3.2 3.2 6.4 6.4 0 0 0 3.2 3.2 3.2-6.4 6.4-6.4 9.6-6.4 12.8v3.2h3.2c3.2 0 3.2 3.2 6.4 3.2 6.4 0 9.6-3.2 16-3.2 3.2-3.2 3.2-6.4 6.4-6.4 0-3.2 3.2-3.2 3.2-6.4 3.2-3.2 3.2-3.2 9.6-3.2l9.6-9.6h3.2l3.2-3.2 3.2 3.2v6.4l-3.2 3.2V192c3.2 3.2 3.2 9.6 0 12.8h-3.2c-3.2 16 0 35.2-3.2 51.2 0 0 0 3.2-3.2 6.4 0 0-3.2 0-3.2 3.2-6.4 0-12.8 0-19.2 3.2v3.2c-3.2 3.2-6.4 3.2-6.4 6.4v6.4c6.4 3.2 9.6 6.4 9.6 12.8 3.2 6.4 3.2 12.8 3.2 22.4-3.2 3.2-3.2 6.4-6.4 6.4 0 3.2 0 9.6-3.2 12.8h-3.2v-3.2s-3.2-3.2-6.4-3.2c-3.2 3.2-3.2 6.4-6.4 12.8-3.2 3.2-9.6 6.4-12.8 12.8 0 3.2 3.2 3.2 3.2 3.2 0 3.2-3.2 3.2-3.2 6.4l-3.2 3.2c-6.4 3.2-16 0-22.4 0-3.2 0-3.2 0-3.2 3.2s-3.2 3.2-3.2 6.4l-3.2 3.2-3.2 3.2c0 3.2-3.2 3.2-6.4 9.6v3.2c-3.2 3.2-3.2 6.4-3.2 6.4-3.2 0-6.4 3.2-6.4 6.4-6.4 6.4-12.8 9.6-22.4 6.4-3.2 0-3.2 0-6.4 3.2 0 3.2 0 3.2-3.2 6.4 0 0 0 3.2-3.2 3.2v6.4l-6.4 9.6s0 3.2-3.2 3.2h-3.2l-3.2 3.2c-3.2 0-6.4 3.2-9.6 3.2 0-3.2-3.2-3.2-3.2-6.4v-6.4l3.2-3.2c3.2-6.4 0-16 3.2-25.6l3.2-3.2c3.2 0 0-3.2 3.2-6.4v-9.6c-3.2 0-3.2 3.2-6.4 3.2h-6.4c-3.2 3.2-3.2 6.4-3.2 9.6 0 3.2-3.2 3.2-3.2 3.2s0 3.2-3.2 6.4c-3.2 0-6.4 3.2-9.6 6.4-3.2 3.2-6.4 9.6-9.6 12.8 0 3.2-3.2 3.2 0 3.2l-6.4 6.4c-3.2 0-3.2 0-6.4 3.2h-6.4-3.2c-3.2 0 0 3.2 0 6.4s3.2 6.4 6.4 6.4h12.8c3.2 0 9.6 3.2 12.8 6.4v6.4h-3.2l3.2 3.2h6.4l9.6-9.6h3.2c3.2-3.2 12.8-9.6 19.2-9.6l3.2 3.2c3.2 0 3.2 0 9.6 3.2h3.2c3.2 0 3.2 3.2 9.6 3.2h3.2c3.2 0 3.2 0 6.4 3.2v3.2c-3.2 6.4-9.6 9.6-19.2 12.8 0 0-6.4 0-9.6 3.2 0 3.2 0 6.4-3.2 6.4h-3.2c0 3.2-3.2 3.2-3.2 6.4-3.2 3.2-9.6 6.4-12.8 12.8-3.2 0-3.2 3.2-3.2 6.4-3.2 0-3.2 3.2-3.2 6.4s3.2 3.2 3.2 6.4c3.2 3.2 6.4 0 9.6 0 3.2 3.2 6.4 0 6.4 3.2h3.2c3.2 0 0 6.4 3.2 9.6l3.2 3.2c0 6.4 3.2 16 3.2 22.4 3.2 3.2 6.4 3.2 9.6 6.4 3.2 3.2 3.2 6.4 6.4 9.6l3.2 3.2c0 3.2 3.2 6.4 3.2 9.6 3.2 3.2 3.2 6.4 6.4 6.4l3.2 3.2-9.6 9.6c-3.2 0-3.2 3.2-6.4 3.2h-3.2l-3.2 3.2h12.8l9.6 9.6c0 6.4 3.2 12.8 3.2 19.2l-3.2 3.2c-3.2 3.2 0 9.6 0 16 0 0-3.2 3.2-6.4 3.2-3.2 3.2-3.2 6.4-6.4 9.6 0 6.4-3.2 9.6-6.4 16 0 3.2-3.2 3.2-3.2 9.6 0 3.2-3.2 3.2-3.2 6.4v6.4s-3.2 0-3.2 3.2c0 0 0 3.2-3.2 3.2v12.8c0 3.2-3.2 6.4-3.2 9.6v6.4l-3.2 3.2c-3.2 0-3.2 6.4-6.4 9.6l-3.2 3.2h-6.4l-3.2 3.2v6.4l-3.2 3.2c-3.2 3.2-3.2 6.4-9.6 9.6h-3.2c0 3.2 3.2 0 3.2 3.2h-6.4c0 3.2-6.4 3.2-6.4 6.4s-3.2 6.4-6.4 9.6h-3.2c-3.2 3.2-9.6 6.4-16 6.4l-3.2 3.2h-12.8c-3.2 0-6.4 6.4-12.8 6.4h-3.2c-3.2 0-3.2 3.2-6.4 3.2v-3.2h-3.2s0 3.2-3.2 3.2l-12.8 22.4h-6.4c-3.2 0-6.4 3.2-6.4 3.2 0 3.2-3.2 3.2-3.2 6.4-3.2 3.2-9.6 0-12.8 3.2v3.2h-6.4c-3.2 0-3.2 3.2-9.6 6.4h-9.6c-3.2 0-3.2 3.2-3.2 3.2h-6.4l-3.2 6.4h3.2s3.2 3.2 3.2 6.4-3.2 6.4-3.2 9.6c0 0-3.2 0-6.4 3.2 6.4 3.2 12.8 0 19.2 3.2l3.2 3.2v6.4h-3.2c-3.2 0-3.2 3.2-6.4 6.4 0 3.2 3.2 9.6 0 12.8H675.2v3.2c-3.2 3.2-6.4 3.2-9.6 3.2 0 3.2-3.2 0-3.2 3.2l-16 3.2z m0 0",
  },
  {
    name: "Overseas",
    svgPath:
      "M512 82.6C274.8 82.6 82.6 274.8 82.6 512S274.8 941.4 512 941.4 941.4 749.1 941.4 512 749.2 82.6 512 82.6z m235.2 557.2c-28.1 22.2-27.6 57.4-27.6 57.4C701 711.1 600 791.3 612.1 820.6c-12.1 16.7 1.8 34.4-12.1 42.9-22.7 13.8-56.8-46.6-28.4-53.9-4.9-5.2-10.4-15.9 0-32.5 4.6-7.3 2.8-47.6 5.9-90.7-118.9-86.8 1.9-115-21.9-128.4-7.9-4.4-21.4-23.9-45.2-28.2-19.4-3.5-41.9 8.5-45.7-9.6-20.7-57.9-46.3-26-52.5-41.3-11.9-29.2-86.2-93.1-75.8-133.8 8-31.1-73.5-56.8-113.7-21.6 16.1-18.9 15.7-99.6 58.7-91.1 35.3 7 58.5-8.4 79.7-5.4 42.6 5.9 24.3 15.6 63.9 25.5 22.4 5.6 69.7-20 103.9-9 8.9 2.9 18.4 17.6 7.5 33.1-9.1 12.8-58 11.6-39.9 27.2 15.4 13.2 43.6 36.5 67.2 41.4 23.8 4.9-2.2-54.7 20.3-60.9 22.6-6.2 99.7 54.9 89.8 60.9-23.3 13.9 32.9 16.9 24.1 25.8-3.7 3.7-67.7-21.6-71.9-12.4-11.6 25.6 23 8.7 15.5 28.8-2 5.4-14.7 1.3-21.1 6.6-17.9 14.7-38.9 37.3-42.8 40.6-21.4 18.2-7 28.9-13.9 34.7-6 5.1-14.6-17.5-20.4-12.9-19.2 15.4-50.3 10.5-50.3 22.6 0 12.1 32 12.5 43.5 18.5 14.6 7.7 7.9 22.7 11.7 22.7 23.5 0 45 40.9 45 40.9s16.7 15.8 41.9 13.6c65.6-5.6 67.1 39.7 67.1 39.7 23.9-7.9 59 14.4 45 25.4z",
  },
];
const selectedGeo = inject("selectedGeo");
const updateSelectedGeo = inject("updateSelectedGeo");
const hoveredGeo = ref("China");
const mapMinColor = "#c9bbc1";
const mapMaxColor = "#c20051";
const drawMap = () => {
  const width = 700,
    height = 430;
  const colorLevelMap = d3.interpolateRgb(
    d3.rgb(mapMinColor),
    d3.rgb(mapMaxColor)
  );
  const projection = geo
    .geoMercator()
    .center([107, 39])
    .scale(500)
    .translate([width / 2, height / 2]);
  const path = geo.geoPath(projection);

  const maxValue = Math.max(...Object.values(sexismRatioMap));
  const minValue = Math.min(...Object.values(sexismRatioMap));
  const handleClickGeo = (newName) => {
    if (selectedGeo.value === "China" || selectedGeo.value === "Overseas") {
      d3.select("#" + selectedGeo.value).style(
        "fill",
        colorLevelMap(sexismRatioMap[selectedGeo.value])
      );
    } else {
      d3.select("#" + selectedGeo.value).style("stroke-width", "0px");
    }
    updateSelectedGeo(newName);
    if (newName === "China" || newName === "Overseas") {
      d3.select("#" + newName).style("fill", "gold");
    } else {
      d3.select("#" + newName).style("stroke-width", "3px");
    }
  };
  const handleHoverGeo = (eventData, newName) => {
    hoveredGeo.value = newName;
    if (newName === "China" || newName === "Overseas") {
      d3.select("#" + newName).style("fill", "gold");
    } else {
      d3.select("#" + newName).style("stroke-width", "3px");
    }

    var yPosition = eventData.offsetY + 20;
    var xPosition = eventData.offsetX + 20;
    // 将浮层位置设置为鼠标位置
    var tooltip = d3
      .select(".tooltip")
      .style("left", xPosition + "px")
      .style("top", yPosition + "px");
    // 更新浮层内容
    tooltip.select(".name").text(newName + ": " + sexismRatioMap[newName]);
    // 移除浮层hidden样式，展示浮层
    tooltip.classed("hidden", false);
  };
  const handleHoverLeaveGeo = () => {
    if (hoveredGeo.value !== selectedGeo.value) {
      if (hoveredGeo.value === "China" || hoveredGeo.value === "Overseas") {
        d3.select("#" + hoveredGeo.value).style(
          "fill",
          colorLevelMap(sexismRatioMap[hoveredGeo.value])
        );
      } else {
        d3.select("#" + hoveredGeo.value).style("stroke-width", "0px");
      }
    }
    d3.select(".tooltip").classed("hidden", true);
  };
  //   china map
  d3.select("#chinaMap")
    .append("svg")
    .attr("width", width)
    .attr("height", height)
    .append("g")
    .selectAll("path")
    .data(chinaGeoData.features)
    .enter()
    .append("path")
    .attr("id", function (d, i) {
      return d.properties.name;
    })
    .style("cursor", "pointer")
    .on("click", function (d, i) {
      handleClickGeo(this.id);
    })
    .on("mouseover", function (d, i) {
      handleHoverGeo(d, this.id);
    })
    .on("mouseout", function (d, i) {
      handleHoverLeaveGeo();
    })
    .style("fill", function (d, i) {
      const normalizedRatio =
        (sexismRatioMap[d.properties.name] - minValue) / (maxValue - minValue);
      return colorLevelMap(normalizedRatio);
    })
    .style("stroke", "gold")
    .style("stroke-width", "0px")
    .attr("d", path);
  // extra svgs: china and overseas
  const icons = d3
    .select("#chinaMap")
    .select("svg")
    .append("g")
    .selectAll("path")
    .data(extraGeo)
    .enter();
  icons
    .append("text")
    .attr("x", function (d, i) {
      return i * 45 + 5;
    })
    .attr("y", function (d, i) {
      return 70;
    })
    .text(function (d, i) {
      return d.name;
    });
  icons
    .append("g")
    .attr("transform", "scale(0.05)")
    .append("path")
    .attr("d", function (d, i) {
      return d.svgPath;
    })
    .attr("id", function (d, i) {
      return d.name;
    })
    .style("cursor", "pointer")
    .attr("transform", function (d, i) {
      return `translate(${i * 1024}, 0)`;
    })
    .style("fill", function (d, i) {
      if (d.name === hoveredGeo.value) {
        return "gold";
      } else {
        return colorLevelMap(sexismRatioMap[d.name]);
      }
    })
    .on("mouseover", function (d, i) {
      handleHoverGeo(d, this.id);
    })
    .on("click", function (d, i) {
      handleClickGeo(this.id);
    })
    .on("mouseout", function (d, i) {
      handleHoverLeaveGeo();
    });
};
onMounted(() => {
  drawMap();
});
const showSectionInfo = ref(false);
</script>

<template>
  <div style="width: 100%; height: 100%">
    <div class="box-header" style="justify-content: space-between">
      <div class="box-title">Map View</div>
      <div class="question-logo" @click="() => (showSectionInfo = true)">
        <img src="../assets/question.svg" />
      </div>
    </div>

    <div class="map-outer">
      <div id="chinaMap"></div>
    </div>
    <div style="display: flex; justify-content: space-between">
      <div style="display: flex; align-items: center">
        <div
          class="legend-square"
          :style="'background-color: ' + mapMaxColor"
        ></div>
        <div>high Sexist ratio</div>
        <div
          class="legend-square"
          :style="'background-color: ' + mapMinColor"
        ></div>
        <div>low Sexist ratio</div>
      </div>
      <div style="font-weight: bold">selected region: {{ selectedGeo }}</div>
    </div>
    <a-modal
      v-model:visible="showSectionInfo"
      :footer="null"
      title="Geospatial Analysis"
      @ok="() => (showSectionInfo = false)"
    >
      <p>
        • The regions on the map are hoverable and clickable, and you are free
        to choose any province/municipality/area to further focus on the
        corresponding text analysis of that region. In the upper left corner,
        you can select China as a whole or Overseas. <br />• We calculated the
        sexist ratio for each region, which equals the number of posts predicted
        as Sexism divided by the number of all posts published by the users from
        that region. <br />• The ratios are encoded as the shading on the map:
        the darker shading indicates the higher sexist ratio.
      </p>
    </a-modal>
    <div class="tooltip hidden">
      <b class="name"></b>
    </div>
  </div>
</template>

<style lang="less" scoped>
.map-outer {
  width: 100%;
  height: 90%;
}
.legend-square {
  width: 16px;
  height: 16px;
  margin: 0 4px;
}
.tooltip {
  position: absolute;
  height: auto;
  padding: 5px 10px;
  box-sizing: border-box;
  background-color: white;
  border: 3px solid #c92268;
  opacity: 0.9;
  border-radius: 5px;
  box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.4);
  pointer-events: none;
}
.tooltip.hidden {
  display: none;
}
</style>
