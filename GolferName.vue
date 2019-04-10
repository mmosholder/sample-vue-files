<template>
  <p class="text-40-script text-dark">{{ formatName(setGolfer(golferId).name) }}</p>
</template>

<script>
import { getGolfer, formatName } from "../helpers.js";

// To Do -- Extract golfers out to vuex
export default {
  props: {
    golferId: {
      type: Number,
      required: true
    },
    golfers: {
      type: Array,
      required: true
    }
  },

  methods: {
    setGolfer(id) {
      return getGolfer(id, this.golfers);
    },

    formatName(name) {
      return formatName(name);
    }
  }
};
</script>

