<template>
  <section>
    <div class="gc-reg-header gc-reg-header-center xs-pb-30 xs-nmb-0 sm-pb-200 sm-nmb-110 xs-nmt-30 sm-nmt-50">
      <div class="container">
        <div class="row">
          <div class="col-12 xs-pt-30 sm-pt-100">
            <h1 class="text-90-script text-white">One And Done Dashboard</h1>
          </div>
        </div>
      </div>
      <div class="dark-filter"></div>
      <div class="golf-filter"></div>
    </div>
    <div class="container gc-oad-container">
      <div class="row">
        <div class="col-12 xs-mb-50 lg-mb-100 gc-oad">
          <div class="gc-reg-card">
            <OadHeader v-if="teams.length > 1"
              :teams="teams"
              :activeEntry="activeEntry"
              @setActives="setActiveEntryInfo"
            />
            <div class="gc-oad-body fill-white">
              <GolfCart v-if="networkStatus < 7" :status="networkStatus" />
              <PickForm
                v-else
                :pickItem="activeEntry"
                :currentTournament="currentTournament"
                :listGolfers="golfersList"
                :allGolfers="tournamentGolfers"
                :weekEntries="currentWeekPicks"
                :index="activeIndex"
                :usedGolfers="usedGolfersList"
              />
            </div>
          </div>
        </div>
        <OadPicksRoundup
          v-if="currentWeekPicks.length"
          :currentWeekPicks="currentWeekPicks"
          :golfers="tournamentGolfers"
        />
        <div class="xs-pr-15 xs-pl-15 sm-pl-0 sm-pr-0">
          <div class="col-12 col-md-8 xs-mt-30">
            <h2 class="title-4 text-underline text-dark xs-mb-20">OAD Guide</h2>
            <p class="text-18 text-dark">Welcome to the 2019 Gup's Corner One And Done -- we are so glad you are part of the community. We wanted to be sure you were set with our
              brand new picking interface, so hopefully all of your questions can be answered below. If not, feel free to reach out to <a href="https://twitter.com/GWBuffalo7">@GWBuffalo7</a> or <a href="https://twitter.com/BeerAndBlank">@BeerAndBlank</a>
              on Twitter, or fire an email out to jbpowerranking@gmail.com with any questions or concerns.
            </p>
            <p class="xs-mt-20"><a href="/downloads/2019-PGA-OAD-Rules.pdf" class="text-22-bold no-decoration text-teal link-teal" target="_blank">OAD Rules</a></p>
            <p class="xs-mt-20 xs-mb-10 text-22-bold text-teal">Are my picks saved?</p>
            <p class="text-18 text-dark">Right off the bat, we wanted to be very clear about whether or not your picks have been saved to the database. You'll (hopefully) notice some text under your fancy cursive team name -- here's where you see if your picks are saved or not. When you do save a previously un-picked entry, you'll see a small success message
            at the top of your screen, and the saved/not saves text should show 'saved' and turn green immediately.</p>
            <p class="xs-mt-20 xs-mb-10 text-22-bold text-teal">Can I accidentally pick a previously used golfer?</p>
            <p class="text-18 text-dark"><strong>Definitely not. </strong>However, if you feel there is an error in the list of golfers being presented to you for picking from, please get in touch with us and we'll resolve the issue quickly.
            If you do happen to try to save a golfer in your list who you have taken before, you will be shown an error message and the pick will not be saved.</p>
          </div>
        </div>
      </div>
    </div>
    <LoginModal
      v-if="showLoginModal"
      :show="showLoginModal"
      @close="showLoginModal = false"
    ></LoginModal>
  </section>
</template>
<script>
import { USER_ID } from "../../constants/settings";
import { mapMutations, mapGetters } from "vuex";
import { checkIfPicksOpen } from "../../helpers.js";
import GOLFERS_ALL from "../../graphql/GolfersAll.gql";
import TOURNAMENT_GOLFERS from "../../graphql/TournamentGolfersQuery.gql";
import TOURNAMENTS from "../../graphql/TournamentsAll.gql";
import USER_PICKS from "../../graphql/UserPgaPicksAll.gql";
import PickForm from "../../components/OadPickItem";
import GolfCart from "../../global/GolfCartAnimation";
import LoginModal from "../../modals/LoginModal";
import OadHeader from "../../components/OadHeader";
import OadPicksRoundup from "../../components/OadPicksRoundup";

export default {
  components: { PickForm, GolfCart, LoginModal, OadHeader, OadPicksRoundup },

  data() {
    return {
      showLoginModal: false,
      skipTourQuery: true,
      skipTourGolfersQuery: true,
      skipUserPicksQuery: true,
      golfersList: [],
      usedGolfersList: [],
      currentWeekPicks: [],
      teams: [],
      golfersReady: false,
      activeEntry: null,
      networkStatus: null,
      activeIndex: 0,
      showModal: false
    };
  },

  created() {
    if (this.tournaments.length == 0) {
      this.triggerTourQuery();
    }

    if (this.currentTournament) {
      this.triggerTourGolferQuery();
    }
  },

  computed: {
    ...mapGetters(["currentTournament", "tournaments"]),

    userId() {
      return parseInt(localStorage.getItem(USER_ID));
    },

    picksOpen() {
      if (this.currentTournament) {
        return checkIfPicksOpen(this.currentTournament);
      }
    }
  },

  watch: {
    currentTournament() {
      this.triggerTourGolferQuery();
    },

    activeEntry() {
      this.setGolfersList();
      this.setUsedGolfersList();
    },

    golfers() {
      if (this.userPgaPicks && this.golfers) {
        this.setUsedGolfersList();
      }
    },

    showLoginModal() {
      let body = document.getElementsByTagName("body")[0];

      if (this.showLoginModal) {
        body.style.overflowY = "hidden";
      } else {
        body.style.overflowY = "auto";
      }
    }
  },

  methods: {
    ...mapMutations(["set_all_tournaments"]),

    setTournaments(data) {
      this.set_all_tournaments(data.tournaments);
    },

    setActiveEntryInfo(payload) {
      this.activeEntry = payload.team;
      this.activeIndex = payload.index;
    },

    setGolfersList() {
      // clear out used golfers
      this.golfersList = [];

      this.tournamentGolfers.forEach((golfer, i) => {
        const usedGolfer = this.userPgaPicks.find(pick => {
          return pick.tournament_golfer_id == golfer.id && pick.tournament_id != this.currentTournament.id && pick.entry_id == this.activeEntry.entry_id;
        });

        if (!usedGolfer) {
          // Using this hokey way of making a surface copy and not copy reactivity
          const newGolfer = JSON.parse(JSON.stringify(golfer));
          this.golfersList.push(newGolfer);
        }

        if (i + 1 == this.tournamentGolfers.length) {
          this.golfersReady = true;
        }
      });
    },

    setUsedGolfersList() {
      this.usedGolfersList = [];

      this.userPgaPicks.forEach(pick => {
        if (pick.entry_id == this.activeEntry.entry_id) {
          let golfer = this.golfers.find(g => {
            return g.id == pick.tournament_golfer_id && pick.tournament_id != this.currentTournament.id;
          });

          if (golfer) {
            this.usedGolfersList.push(golfer);
          }
        }
      });
    },

    setCurrentWeekPicks(data) {
      this.currentWeekPicks = [];

      data.userPgaPicks.forEach(pick => {
        if (pick.tournament_id == this.currentTournament.id) {
          let curPick = JSON.parse(JSON.stringify(pick));

          if (!curPick.tournament_golfer_id) {
            curPick.pickMade = false;
          }

          if (!curPick.alt_tournament_golfer_id) {
            curPick.altPickMade = false;
          }

          this.currentWeekPicks.push(curPick);
        }
      });
    },

    setTeams(data) {
      if (this.teams.length == 0) {
        const pickArr = data.userPgaPicks.slice(0, 3);

        pickArr.forEach((pick, i) => {
          if (i == 0) {
            const team = {
              entry_id: pick.entry_id,
              teamname: pick.teamname
            };

            this.teams.push(team);
          } else {
            const setTeam = this.teams.find(team => {
              return team.teamname == pick.teamname;
            });
            if (!setTeam) {
              const team = {
                entry_id: pick.entry_id,
                teamname: pick.teamname
              };

              this.teams.push(team);
            }
          }

          if (i + 1 == pickArr.length) {
            this.activeEntry = this.teams[0];
            this.activeIndex = 0;
          }
        });
      }
    },

    triggerTourQuery() {
      this.skipTourQuery = false;
    },

    triggerTourGolferQuery() {
      this.skipTourGolfersQuery = false;
    }
  },

  apollo: {
    tournaments: {
      query: TOURNAMENTS,
      skip() {
        return this.skipTourQuery;
      },
      result({ data, loading, networkStatus }) {
        if (data) {
          this.setTournaments(data);
        }
      }
    },
    tournamentGolfers: {
      query: TOURNAMENT_GOLFERS,
      variables() {
        return {
          tournament_id: this.currentTournament.id
        };
      },
      skip() {
        return this.skipTourGolfersQuery;
      },
      result({ data, networkStatus }) {
        if (data) {
          this.skipUserPicksQuery = false;
        }
      }
    },
    golfers: {
      query: GOLFERS_ALL
    },
    userPgaPicks: {
      query: USER_PICKS,
      options: {
        notifyOnNetworkStatusChange: true
      },
      variables() {
        return {
          user_id: this.userId,
          tournament_id: this.currentTournament.id
        };
      },
      skip() {
        return this.skipUserPicksQuery;
      },
      result({ data, loading, networkStatus }) {
        this.networkStatus = networkStatus;
        if (data) {
          this.setCurrentWeekPicks(data);
          this.setTeams(data);
          this.setGolfersList();
        }
      },
      error(error) {
        if (error) {
          this.showLoginModal = true;
        }
      }
    }
  }
};
</script>
