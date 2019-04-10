<template>
  <section class="col-12 xs-pt-50 xs-pb-50 xs-pr-25 xs-pl-25">
    <div class="row">
      <h2 class="col-12 col-sm-8 col-md-6 text center text-45-script xs-mb-30 sm-mb-50 text-dark">Picks Roundup</h2>
    </div>
    <div class="row xs-mb-30" v-for="team in currentWeekPicks" :key="team.entry_id">
      <div class="col-12 col-sm-4 col-lg-3 xs-mb-20 sm-mb-0" v-if="team.tournament_golfer_id">
        <div class="text-center">
          <p class="text-16-button text-blue-bg text-white xs-mb-10 inline-block">Main</p>
        </div>
        <div class="gc-oad-inner-item -roundup flex align-center justify-center" v-if="team.tournament_golfer_id">
          <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" x="0px" y="0px" viewBox="0 0 64 64" style="enable-background:new 0 0 64 64;" xml:space="preserve"><path d="M42.327,23.146l8.757-7.961C52.302,14.077,53,12.498,53,10.854C53,7.626,50.374,5,47.146,5H44c0-1.103,0.897-2,2-2V1H18v2  c1.103,0,2,0.897,2,2h-3.146C13.626,5,11,7.626,11,10.854c0,1.645,0.698,3.224,1.916,4.331l8.757,7.961  C21.881,23.334,22,23.604,22,23.885V24c0,0.552-0.448,1-1,1s-1-0.448-1-1v-1h-2v1c0,1.654,1.346,3,3,3c0.352,0,0.686-0.072,1-0.184  v1.719l6,4v5.185c-1.823,2.953-4.338,5.459-7.292,7.28H19v18h26V45h-1.708c-2.955-1.821-5.47-4.327-7.292-7.28v-5.185l6-4v-1.719  C42.314,26.928,42.648,27,43,27c1.654,0,3-1.346,3-3v-1h-2v1c0,0.552-0.448,1-1,1s-1-0.448-1-1v-0.115  C42,23.604,42.119,23.334,42.327,23.146z M44,12.308V7h3.146C49.271,7,51,8.729,51,10.854c0,1.083-0.46,2.122-1.262,2.851  l-7.553,6.866c0.152-0.92,0.393-1.824,0.743-2.699C43.64,16.093,44,14.221,44,12.308z M42.537,3C42.195,3.589,42,4.272,42,5v7.308  c0,1.658-0.312,3.28-0.929,4.821C40.36,18.907,40,20.779,40,22.692v1.192V24v3H24v-3v-0.115v-1.192c0-1.913-0.36-3.785-1.071-5.563  C22.312,15.588,22,13.966,22,12.308V5c0-0.728-0.195-1.411-0.537-2H42.537z M14.262,13.704C13.46,12.976,13,11.937,13,10.854  C13,8.729,14.729,7,16.854,7H20v5.308c0,1.913,0.36,3.785,1.071,5.563c0.35,0.876,0.591,1.78,0.743,2.699L14.262,13.704z M43,47v2  H21v-2H43z M21,61v-2h18v-2H21v-6h22v6h-2v2h2v2H21z M39.826,45H24.174c2.107-1.685,3.927-3.718,5.372-6h4.909  C35.9,41.282,37.72,43.315,39.826,45z M30,33h4v4h-4V33z M34.697,31h-5.395l-3-2h11.395L34.697,31z"></path></svg>
          <GolferName :golferId="team.tournament_golfer_id" :golfers="golfers" />
        </div>
      </div>
      <div class="col-12 col-sm-4 col-lg-3 xs-mb-20 sm-mb-0" v-if="team.alt_tournament_golfer_id">
        <div class="text-center">
          <p class="text-16-button text-gray-bg text-white xs-mb-10 inline-block">Alternate</p>
        </div>
        <div class="gc-oad-inner-item -roundup  flex align-center justify-center" v-if="team.alt_tournament_golfer_id">
          <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" x="0px" y="0px" viewBox="0 0 64 64" style="enable-background:new 0 0 64 64;" xml:space="preserve"><path d="M38,39c0-1.302-0.839-2.402-2-2.816v-4.061l7.312,3.666l-2.236-12.451l8.98-8.98l-12.386-2.252L32,0.764l-5.671,11.342  l-12.386,2.252l8.98,8.98l-2.236,12.451L28,32.123v4.061c-1.161,0.414-2,1.514-2,2.816v14h-6v10h24V53h-6V39z M24.896,23.67  l4.192-2.096l-5.226,7.859L24.896,23.67z M39.104,23.67l1.035,5.763l-5.226-7.859L39.104,23.67z M37.395,14.088l5.865,1.066  l-8.531,2.133L37.395,14.088z M33,16.238V7.236l2.813,5.626L33,16.238z M31,16.238l-2.813-3.375L31,7.236V16.238z M29.271,17.287  l-8.531-2.133l5.865-1.066L29.271,17.287z M31,22.309v6.074l-6.058,3.036L31,22.309z M33,22.309l6.058,9.11L33,28.383V22.309z   M39.802,21.783l-4.938-2.469l9.876-2.469L39.802,21.783z M19.259,16.845l9.876,2.469l-4.938,2.469L19.259,16.845z M32,30.118  l2,1.003V36h-4v-4.879L32,30.118z M28,42h4v-2h-4v-1c0-0.552,0.449-1,1-1h6c0.551,0,1,0.448,1,1v1h-2v2h2v11h-8V42z M42,57h-2v2h2v2  H22v-2h16v-2H22v-2h20V57z"></path></svg>
          <GolferName :golferId="team.alt_tournament_golfer_id" :golfers="golfers" />
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import GolferName from "@components/GolferName";

export default {
  props: {
    currentWeekPicks: {
      type: Array,
      required: true
    },
    golfers: {
      type: Array,
      required: true
    }
  },

  components: { GolferName }
};
</script>
