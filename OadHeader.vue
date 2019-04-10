<template>
  <section>
    <div class="gc-oad-header sm-mb-10" v-if="teams.length > 1">
      <div
        v-for="(team, i) in teams"
        :class="['gc-oad-header-item', activeEntry.entry_id == team.entry_id ? '-active' : '']"
        :key="team.teamname"
        @click="emitActives(team, i)"
      >
        <p class="text-20-bold text-dark">{{ team.teamname }}</p>
      </div>
    </div>
    <div class="gc-oad-select col-12 xs-pt-20" v-if="teams.length > 1">
      <label class="text-18-semi xs-mb-10">Choose Team</label>
      <div class="select">
        <select v-model="activeEntry">
          <option :value="team" v-for="team in teams" :key="team.entry_id">{{ team.teamname}}</option>
        </select>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  props: {
    teams: {
      type: Array
    },
    activeEntry: {
      type: Object
    }
  },

  methods: {
    emitActives(team, index) {
      this.$emit("setActives", {team: team, index: index});
    }
  }
};
</script>

