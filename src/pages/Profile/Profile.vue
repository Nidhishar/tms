<template>
  <div>
    <h1 class="page-title">Dashboard</h1>
    <Alert360 />

    <b-container class="no-gutters p-0">
      <b-row>
        <b-col lg="8" xs="12">
          <div class="pb-xlg">
            <Widget class="mb-0">
              <div class="h-100">
                <div class="float-left pr-3">
                  <img
                    :src="user.profileImage ? user.profileImage : image"
                    class="rounded-circle"
                    width="75"
                    height="75"
                    alt="..."
                  />
                </div>

                <div class="pt-2">
                  <span class="fs-larger text-capitalize">
                    <span class="fw-semi-bold">{{user.name}}</span>
                  </span>
                  <p class="fw-small">{{user.jobtitle}}</p>
                </div>
              </div>
              <!--==================================================================== 
                * Dashbard blocks 
              *=======================================================================-->
              <div class="w-auto pl-5 ml-5">
                <!-- blocks -->
                <span class="mr-4 fw-semi-bold">Reviewers</span>
                <br />
                <!-- 1st block -->
                <!-- v-bind:class="{ codegreen, codeblue , codeorange }" -->
                <span v-for="(manager,index) in sortedArray" :key="index">
                  <div
                    class="rounded w-auto p-1 h-auto mr-1 mt-2"
                    v-bind:class="{success : manager.weight <= 3 ,primary:  manager.weight > 3 && manager.weight <= 6, warning:  manager.weight > 6 && manager.weight <= 10}"
                  >
                    <span class="thumb-md float-left ml-1 mt-1">
                      <img
                        class="rounded-circle"
                        :src="manager.profileImage ? manager.profileImage : image"
                        width="35"
                        height="35"
                        alt="..."
                      />
                    </span>
                    <div class="float-left text-white text-left pl-2 pr-1">
                      <span class="fw-bold fs-large">
                        {{manager.username | firstname }}
                        <span
                          class="fw-normal"
                        >{{manager.username | lastname }}</span>
                      </span>
                      <br />
                      <span class="fs-sm">{{manager.job_title}}</span>
                    </div>
                  </div>
                </span>
              </div>
            </Widget>
          </div>
          <!-- second widget -->
          <h1>Key Performance Area</h1>
          <div v-if="user.kpi">
            <AreaComponent :eraKpiArray="user.kpi.kpi_json" :monthlyRating="user.Monthly_rating" />
          </div>
          <!-- second widget ends-->
          <!-- third widget -->
          <h1>Extra Resource Area</h1>
          <div v-if="user.kpi">
            <AreaComponent :eraKpiArray="user.kpi.era_json" :monthlyRating="user.Monthly_rating" />
          </div>
          <!-- third widget ends-->
        </b-col>

        <!--==================================================================== 
          * Right overAll Score
        *=======================================================================-->
        <!-- <b-col class="p-0 ml-3"> -->
        <b-col>
          <div class="h-auto pb-3" lg="4" xs="12">
            <h2 class="mb-3">
              Overall
              <span class="fw-semi-bold">Score</span>
            </h2>
            <!-- first bar -->
            <div>
              <div>
                <h6 class="text-dark fs-larger">
                  Check-ins Score
                  <i
                    v-b-popover.hover="`You are expected to do daily checkin's for your work. If you miss a checkin your checkin score will reduce. This score is mainly a percentage of total checkins vs total working days`"
                    class="fas fa-question-circle fs-sm text-danger"
                  ></i>
                  <span class="float-right">{{checkin_rating}}%</span>
                </h6>
                <span class="text-secondary fs-sm">Daily stand-up report submitted</span>
                <b-progress
                  class="w-75"
                  style="height: 5px"
                  variant="primary"
                  :value="user.Checkin_rating"
                  :max="100"
                />
              </div>
            </div>
            <!-- second bar -->
            <div>
              <div>
                <h6 class="text-dark fs-larger">
                  Overall Review
                  <i
                    v-b-popover.hover="`This is the weighted reviewed given by your seniors. This is score is most important and reflects your performance.`"
                    class="fas fa-question-circle fs-sm text-danger"
                  ></i>
                  <span class="float-right">{{Number(Overall_rating).toFixed(2)}} / 10</span>
                </h6>
                <span class="text-secondary fs-sm">Overall performance review</span>
                <b-progress
                  class="w-75"
                  style="height: 5px"
                  variant="success"
                  :value="user.Overall_rating * 10"
                  :max="100"
                />
              </div>
            </div>
            <!-- third bar -->
            <!-- Recent Activities -->
          </div>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import Widget from "@/components/Widget/Widget";
import AreaComponent from "./../../components/Area/Area";
import { get, call, sync } from "vuex-pathify";
import dummyimage from "@/components/Group/person-dummy.jpg";
import Alert360 from "@/components/Alert360/alert360";

export default {
  name: "Profile",
  data() {
    return {
      ratedStar: 1,
      two: 1,
      starSize: "17px",
      image: dummyimage,
      checkin_rating: "0",
      Overall_rating: "0",
      completedSteps: 5,
      totalSteps: 10,
      user: {}
    };
  },
  components: {
    Widget,
    AreaComponent,
    Alert360
  },
  async mounted() {
    if (!Object.keys(this.$route.params).length) {
      await this.get_profile();
      await this.get_activity();
      this.user = this.userProfile;
    }
  },
  created() {
    if (Object.keys(this.$route.params).length) {
      this.fetchData();
    }
  },
  props: {
    userData: {
      type: String,
      default: ""
    }
  },
  computed: {
    userProfile: get("profile/user"),
    activity: get("profile/activity"),
    userToCheckByAdmin_: sync("allMember/userToCheckByAdmin"),
    date() {
      let value = null
      this.activity.forEach(activity => {
        if (activity.missed_checkin) {
          activity.missed_checkin.forEach(missdate => {
            var time = this.$moment(missdate.checkin_message)
              .tz("GMT")
              .local()
              .format("h:mm: A");
            if (time !== "Invalid date") {
              missdate["datemisstime"] =
                this.$moment(missdate.checkin_message).format(
                  " MMMM DD, YYYY"
                ) +
                " at " +
                time;
              missdate["datemiss"] = this.$moment(
                missdate.checkin_message
              ).format(" MMMM DD, YYYY");
            }
          });
        }
      });
      return value
    },
    dailydate() {
      let value = null
      this.activity.forEach(activity => {
        if (activity.Daily_checkin) {
          activity.Daily_checkin.forEach(checkindailydate => {
            var time = this.$moment(checkindailydate.Daily_chechkin_message)
              .tz("GMT")
              .local()
              .format("h:mm: A");
            if (time !== "Invalid date") {
              checkindailydate["dailycheckindate"] = this.$moment(
                checkindailydate.Daily_chechkin_message
              ).format(" MMMM DD, YYYY");
              checkindailydate["dailycheckintime"] =
                this.$moment(checkindailydate.Daily_chechkin_message).format(
                  " MMMM DD, YYYY"
                ) +
                " at " +
                time;
            }
          });
        }
      });
      return value
    },
    reportreview() {
      let value = null
      this.activity.forEach(activity => {
        if (activity.review_report) {
          activity.review_report.forEach(reportreviewdate => {
            var time = this.$moment(reportreviewdate.created_at)
              .tz("GMT")
              .local()
              .format("h:mm: A");
            if (time !== "Invalid date") {
              reportreviewdate["reportreviewtime"] =
                this.$moment(reportreviewdate.created_at).format(
                  " MMMM DD, YYYY"
                ) +
                " at " +
                time;
            }
          });
        }
      });
      return value
    },
    sortedArray() {
      let managers = null
      // eslint-disable-next-line
      this.checkin_rating = this.user.Checkin_rating
        ? Math.round(this.user.Checkin_rating)
        : 0;
      // eslint-disable-next-line
      this.Overall_rating = this.user.Overall_rating
        ? this.user.Overall_rating
        : 0;
      managers = this.user.managers;
      if (managers) {
        // eslint-disable-next-line
        function compare(a, b) {
          if (a.weight && b.weight) {
            if (a.weight > b.weight) return -1;
            if (a.weight < b.weight) return 1;
            return 0;
          }
        }
        managers = managers.sort(compare);
      }
      return managers;
    }
  },
  filters: {
    firstname: function(value) {
      if (value.indexOf(" ") >= 0) {
        return value
          .split(" ")
          .slice(0, -1)
          .join(" ");
      } else return value;
    },
    lastname: function(value) {
      if (value.indexOf(" ") >= 0) {
        return value
          .split(" ")
          .slice(-1)
          .join(" ");
      } else return;
    }
  },

  methods: {
    getProfile: call("profile/getProfile"),
    getActivity: call("profile/getActivity"),
    get_profile: function() {
      this.getProfile();
    },
    get_activity: function() {
      this.getActivity();
    },
    fetchData() {
      this.user = this.userToCheckByAdmin_;
    },
    submitStarRateOne(value) {
      this.ratedStar = value;
    },
    submitStarRateTwo(value) {
      this.two = value;
    }
  }
};
</script>

<style src="./Profile.scss" lang="scss" scoped />
