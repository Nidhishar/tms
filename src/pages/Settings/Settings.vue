<template>
  <div>
    <h1>Settings</h1>
    <b-row class="justify-content-md-center">
      <b-col lg="12" md="12">
        <Widget>
          <b-form-group>
            <b-form @submit.prevent="setSlackSettings()">
              <legend>
                <strong>Slack</strong> tokens
              </legend>
              <b-form-group
                horizontal
                label="Web hook"
                label-for="normal-field-4"
                label-class="text-md-right"
                :label-cols="2"
                breakpoint="md"
              >
                <b-form-input
                  type="text"
                  id="normal-field-4"
                  v-model="slackTokens.webhook_url"
                  required
                />
              </b-form-group>
              <b-form-group
                horizontal
                label-for="normal-field"
                label-class="text-md-right"
                :label-cols="2"
                breakpoint="md"
              >
                <div slot="label">Slack token</div>
                <b-form-input
                  type="text"
                  v-model="slackTokens.slack_token"
                  id="label-hint"
                  required
                />
              </b-form-group>
              <b-form-group
                horizontal
                label-for="normal-field"
                label-class="text-md-right"
                :label-cols="2"
                breakpoint="md"
              >
                <div slot="label">HR Secret key</div>
                <b-form-input
                  type="text"
                  v-model="slackTokens.secret_key"
                  id="label-hint"
                  required
                />
              </b-form-group>
              <b-form-group
                horizontal
                label
                label-for="transparent-field"
                :label-cols="2"
                breakpoint="md"
              >
                <b-button variant="primary" type="submit" class="mr-xs w-28">
                  <span v-if="loadingSlackToken">
                    <i class="fa fa-circle-o-notch fa-spin"></i>
                  </span>
                  <span v-else>Save Changes</span>
                </b-button>
              </b-form-group>
            </b-form>
          </b-form-group>
        </Widget>
      </b-col>
    </b-row>
    <b-row class="justify-content-md-center">
      <b-col md="12">
        <Widget customHeader>
          <b-row>
            <b-col lg="12">
              <b-form @submit.prevent="setSchedularMsg()">
                <legend>
                  <strong>Reminder</strong> messages
                </legend>
                <b-row class="justify-content-md-center">
                  <b-col cols="6" sm="12" lg="6" md="6">
                    <div class="form-group">
                      <div class="row">
                        <div class="col-md-6 col-6 text-md-right">
                          <label for="normal-field">Monthly reminder</label>
                        </div>
                        <div class="col-md-6 col-6">
                          <input
                            @change="setSchedularSettings()"
                            class="apple-switch form-control"
                            v-model="schedularSettings.monthly_remainder"
                            type="checkbox"
                          />
                        </div>
                      </div>
                    </div>
                  </b-col>
                  <b-col cols="6" sm="12" lg="6" md="6">
                    <div class="form-group">
                      <div class="row">
                        <div class="col-md-6 text-md-right">
                          <label for="normal-field-week">Weekly reminder01</label>
                        </div>
                        <div class="col-6">
                          <input
                            @change="setSchedularSettings()"
                            class="apple-switch form-control"
                            v-model="schedularSettings.weekly_remainder"
                            type="checkbox"
                          />
                        </div>
                      </div>
                    </div>
                  </b-col>
                </b-row>
                <b-row class="justify-content-md-center">
                  <b-col cols="6" sm="12" lg="6" md="6">
                    <div class="form-group">
                      <div class="row">
                        <div class="col-md-6 col-6 text-md-right">
                          <label for="normal-field-review">Review activity</label>
                        </div>
                        <div class="col-md-6 col-6">
                          <input
                            @change="setSchedularSettings()"
                            class="apple-switch form-control"
                            v-model="schedularSettings.review_activity"
                            type="checkbox"
                          />
                        </div>
                      </div>
                    </div>    
                  </b-col>
                  <b-col cols="6" sm="12" lg="6" md="6">
                    <div class="form-group">
                      <div class="row">
                        <div class="col-md-6 col-6 text-md-right">
                          <label for="normal-field-month">Monthly manager reminder</label>
                        </div>
                        <div class="col-md-6 col-6">
                          <input
                            @change="setSchedularSettings()"
                            class="apple-switch form-control"
                            v-model="schedularSettings.monthly_manager_reminder"
                            type="checkbox"
                          />
                        </div>
                      </div>
                    </div>    
                  </b-col>
                </b-row>
                <b-row class="justify-content-md-center">
                  <b-col cols="6" sm="12" lg="6" md="6">
                    <div class="form-group">
                      <div class="row">
                        <div class="col-md-6 col-6 text-md-right">
                          <label for="normal-field-month">Missed checkin</label>
                        </div>
                        <div class="col-md-6 col-6">
                          <input
                            @change="setSchedularSettings()"
                            class="apple-switch form-control"
                            v-model="schedularSettings.recent_activity"
                            type="checkbox"
                          />
                        </div>
                      </div>
                    </div>
                  </b-col>
                  <b-col cols="6" sm="12" lg="6" md="6">
                    <div class="form-group">
                      <div class="row">
                        <div class="col-md-6 col-6 text-md-right">
                          <label for="normal-field-month">Weekly missed review message</label>
                        </div>
                        <div class="col-md-6 col-6">
                          <input
                            @change="setSchedularSettings()"
                            class="apple-switch form-control"
                            v-model="schedularSettings.missed_reviewed"
                            type="checkbox"
                          />
                        </div>
                      </div>
                    </div>    
                  </b-col>
                </b-row>
                <b-row class="justify-content-md-center">
                  <b-col cols="6" sm="12" lg="6" md="6">
                    <div class="form-group">
                      <div class="row">
                        <div class="col-md-6 col-6 text-md-right">
                          <label for="normal-field-week">Make 360&deg; mandatory</label>
                        </div>
                        <div class="col-md-6 col-6">
                          <input
                            @change="setSchedularSettings()"
                            v-model="schedularSettings.revew_360_setting"
                            class="apple-switch form-control"
                            type="checkbox"
                          />
                        </div>
                      </div>
                    </div>
                  </b-col>
                  <b-col cols="6" sm="12" lg="6" md="6">
                    <div class="form-group">
                      <div class="row">
                        <div class="col-md-6 col-6 text-md-right">
                          <label for="normal-field-week">Skip Weekly Review&deg; for manager</label>
                        </div>
                        <div class="col-md-6 col-6">
                          <input
                            @change="setSchedularSettings()"
                            v-model="schedularSettings.managerSkip"
                            class="apple-switch form-control"
                            type="checkbox"
                          />
                        </div>
                      </div>
                    </div>
                  </b-col>
                </b-row>
                <b-row class="justify-content-md-center">
                  <b-col cols="6" sm="12" lg="6" md="6">
                    <div class="form-group" v-if="schedularSettings.managerSkip === true">
                      <div class="row">
                          <div class="col-md-6 col-6 text-md-right"> 
                          <label for="normal-field-week">Only Manager Can Skip</label>
                        </div>
                        <div class="col-md-6 col-6">
                          <input
                            @change="setSchedularSettings()"
                            v-model="schedularSettings.only_manager_skip"
                            class="apple-switch form-control"
                            type="checkbox"
                          />
                        </div>
                      </div>
                    </div>      
                  </b-col>
                  <b-col cols="6" sm="12" lg="6" md="6">
                    <div class="form-group">
                      <div class="row">
                        <div class="col-md-6 col-6 text-md-right">
                          <label for="normal-field-week">Automate Weekly</label>
                        </div>
                        <div class="col-md-6 col-6">
                          <input
                            @change="setSchedularSettings()"
                            v-model="schedularSettings.weekly_automated"
                            class="apple-switch form-control"
                            type="checkbox"
                          />
                        </div>
                      </div>
                    </div>      
                  </b-col>
                </b-row>
                <b-row class="justify-content-md-center">
                  <b-col cols="6" sm="12" lg="6" md="6">
                    <div class="form-group">
                      <div class="row">
                        <div class="col-md-6 col-6 text-md-right">
                          <label for="normal-field-week">Enable easy rating </label>
                        </div>
                        <div class="col-md-6 col-6">
                          <input
                            @change="setSchedularSettings()"
                            v-model="schedularSettings.easyRating"
                            class="apple-switch form-control"
                            type="checkbox"
                          />
                        </div>
                      </div>
                    </div>
                  </b-col>
                  <b-col>
                  </b-col>
                </b-row>
              </b-form>
            </b-col>
          </b-row>
        </Widget>
      </b-col>
    </b-row>
     <b-row class="justify-content-md-center">
      <b-col md="12">
        <Widget customHeader>
          <b-row>
            <b-col lg="12">
              <!-- <b-form @submit.prevent="setReportsSetting()"> -->
                <legend>
                  <strong>Weekly / Monthly </strong> setting
                </legend>
                <div class="form-group">
                  <div class="row">
                    <div class="col-md-2 col-sm-12 text-md-right">
                      <label for="normal-field-week">Disable Monthly Reports</label>
                    </div>
                    <div class="col-8 col-md-5">
                      <input
                        @change="setMonthlyReportsSetting()"
                        v-model="reportsSetting.monthly_status"
                        class="apple-switch form-control"
                        type="checkbox"
                      />
                    </div>
                  </div>
                </div>
                <div class="form-group">
                  <div class="row">
                      <div class="col-md-2 col-sm-12 text-md-right"> 
                      <label for="normal-field-week">Disable Weekly Reports</label>
                    </div>
                    <div class="col-8 col-md-5">
                      <input
                        @change="setWeeklyReportsSetting()"
                        v-model="reportsSetting.weekly_status"
                        class="apple-switch form-control"
                        type="checkbox"
                      />
                    </div>
                  </div>
                </div>
              <!-- </b-form> -->
            </b-col>
          </b-row>
        </Widget>
      </b-col>
    </b-row>
  </div>
</template>

<script>
import Widget from "@/components/Widget/Widget";
import { call, sync } from "vuex-pathify";
export default {
  components: {
    Widget
  },
  created() {
    this.getSlackSettings();
    this.getSchedularSettings();
    this.getSchedularMsg();
  },
  mounted () {
    this.reportsSetting.monthly_status = this.userProfile.monthly_status,
    this.reportsSetting.weekly_status = this.userProfile.weekly_status
  },
  computed: {
    userProfile: sync("profile/user")
  },
  data() {
    return {
      loadingSlackToken: false,
      loadingReminderMessage: false,
      loaderSchedularSettings: false,
      slackTokens: {
        webhook_url: "",
        slack_token: "",
        secret_key: ""
      },
      reminderMessage: {
        monthly_remainder: "",
        weekly_remainder1: "",
        weekly_remainder2: "",
        review_activity: "",
        monthly_manager_reminder: "",
        missed_checkin: "",
        weekly_report_mesg: "",
        monthly_report_mesg: "",
        missed_reviewed_mesg: ""
      },
      schedularSettings: {
        monthly_manager_reminder: false,
        monthly_remainder: false,
        recent_activity: false,
        review_activity: false,
        weekly_remainder: false,
        revew_360_setting: false,
        managerSkip: false,
        missed_reviewed: false,
        weekly_automated: false,
        easyRating: false,
        only_manager_skip: false
      },
      reportsSetting: {
        monthly_status: false,
        weekly_status: false
      }
    };
  },
  methods: {
    api_getSlackSettings: call("settings/slackSettings"),
    api_setSlackSettings: call("settings/setSlackSettings"),
    api_getSchedularSettings: call("settings/schedularSettings"),
    api_setSchedularSettings: call("settings/setSchedularSettings"),
    api_getSchedularMsg: call("settings/schedularMsg"),
    api_setSchedularMsg: call("settings/setSchedularMsg"),
    api_setReportsSetting: call("settings/setReportsSetting"),
    getSlackSettings() {
      this.api_getSlackSettings()
        .then(res => {
          this.slackTokens.webhook_url = res.data[0].webhook_url;
          this.slackTokens.slack_token = res.data[0].slack_token;
          this.slackTokens.secret_key = res.data[0].secret_key;
        })
        .catch(err => console.log(err));
    },
    setSlackSettings() {
      this.loadingSlackToken = true;
      this.api_setSlackSettings(this.slackTokens)
        .then(() => {
          this.loadingSlackToken = false;
        })
        .catch(err => {
          console.log(err);
          this.loadingSlackToken = false;
        });
    },
    getSchedularSettings() {
      this.api_getSchedularSettings()
        .then(res => {
          this.schedularSettings.managerSkip = res.data[0].skip_review
          this.schedularSettings.missed_reviewed = res.data[0].missed_reviewed
          this.schedularSettings.monthly_manager_reminder =
            res.data[0].monthly_manager_reminder;
          this.schedularSettings.monthly_remainder =
            res.data[0].monthly_remainder;
          this.schedularSettings.recent_activity = res.data[0].recent_activity;
          this.schedularSettings.review_activity = res.data[0].review_activity;
          this.schedularSettings.weekly_remainder =
            res.data[0].weekly_remainder;
          this.schedularSettings.revew_360_setting =
            res.data[0].revew_360_setting;
          this.schedularSettings.weekly_automated = res.data[0].weekly_automated
          this.schedularSettings.easyRating = res.data[0].easyRating
          this.schedularSettings.only_manager_skip = res.data[0].only_manager_skip
        })
        .catch(err => {
          console.log(err);
        });
    },
    setSchedularSettings() {
      this.loaderSchedularSettings = true;
      this.api_setSchedularSettings(this.schedularSettings)
        .then(() => {
          this.userProfile.easyRating = this.schedularSettings.easyRating
          this.loaderSchedularSettings = false;
        })
        .catch(err => {
          this.loaderSchedularSettings = false;
          console.log(err);
        });
    },
    getSchedularMsg() {
      this.api_getSchedularMsg()
        .then(res => {
          this.reminderMessage.missed_reviewed_mesg = res.data[0].missed_reviewed_mesg
          this.reminderMessage.monthly_remainder =
            res.data[0].monthly_remainder;
          this.reminderMessage.weekly_remainder1 =
            res.data[0].weekly_remainder1;
          this.reminderMessage.weekly_remainder2 =
            res.data[0].weekly_remainder2;
          this.reminderMessage.review_activity = res.data[0].review_activity;
          this.reminderMessage.monthly_manager_reminder =
            res.data[0].monthly_manager_reminder;
          this.reminderMessage.missed_checkin = res.data[0].missed_checkin;
          this.reminderMessage.weekly_report_mesg =
            res.data[0].weekly_report_mesg;
          this.reminderMessage.monthly_report_mesg =
            res.data[0].monthly_report_mesg;
        })
        .catch(err => {
          console.log(err);
        });
    },
    setSchedularMsg() {
      this.loadingReminderMessage = true;
      this.api_setSchedularMsg(this.reminderMessage)
        .then(() => {
          this.getSchedularMsg()
          this.loadingReminderMessage = false;
        })
        .catch(err => {
          console.log(err);
          this.loadingReminderMessage = false;
        });
    },
    async setReportsSetting () {
      let response = await this.api_setReportsSetting(this.reportsSetting)
      if (response.status === 200 && response.data.status === 'success') {
        this.userProfile.monthly_status = this.reportsSetting.monthly_status
        this.userProfile.weekly_status = this.reportsSetting.weekly_status
      }
    },
    setMonthlyReportsSetting () {
      if (this.reportsSetting.weekly_status === true) this.reportsSetting.weekly_status = false
      this.setReportsSetting()
    },
    setWeeklyReportsSetting () {
      if (this.reportsSetting.monthly_status === true) this.reportsSetting.monthly_status = false
      this.setReportsSetting()
    }
  }
};
</script>

<style src="./Settings.scss" lang="scss" />
