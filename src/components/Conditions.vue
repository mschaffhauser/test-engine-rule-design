<template>
  <div class="and-or-template col-xs-12" :class="isFirst ? 'and-or-first' : '' ">
    <v-layout class="mb-2">
      <v-flex xs5>
        <v-card flat>
    <v-card-text>
          <v-btn-toggle v-model="toggle_one">
              <v-btn :color="toggle_one == 0 ? 'primary' : ''" flat>
               And
              </v-btn>
              <v-btn :color="toggle_one == 1 ? 'primary' : ''" flat>
               Or
              </v-btn>
            </v-btn-toggle>
         </v-card-text>
        </v-card>
        <!-- <v-btn :color="isAnd ? 'primary' : ''" @click.prevent="clickAnd">&nbsp;And&nbsp;</v-btn>
        <v-btn :color="!isAnd ? 'primary' : ''" @click.prevent="clickOr">&nbsp;Or&nbsp;</v-btn> -->
      </v-flex>

      <v-flex xs7>
        <v-btn v-if="!isFirst" class="right" @click.prevent="deleteSelf()">
          <i class="fa fa-fw fa-close"></i>
        </v-btn>
        <v-btn class="btn btn-xs btn-purple pull-right" @click.prevent="addGroup">+ ( Rules group )</v-btn>
        <v-btn
          class="btn btn-xs btn-purple add-rule pull-right"
          @click.prevent="addRule"
        >+ Add more conditions</v-btn>
      </v-flex>
    </v-layout>

    <rule
      v-for="(rule, index) in rules"
      ref="rules"
      :options="options"
      :key="rule"
      @delete-rule="deleteRule(index)"
    ></rule>

    <conditions
      class="and-or-offset col-xs-11"
      v-for="(group, index) in groups"
      ref="groups"
      :options="options"
      :key="group"
      @delete-group="deleteGroup(index)"
    ></conditions>
  </div>
</template>

<script>
import Rule from "./Rule";

export default {
  name: "Conditions",
  components: {
    Rule
  },
  props: {
    options: {
      type: Object,
      required: true
    },
    isFirst: {
      type: Boolean,
      default: false
    }
  },
  created() {
    this.addRule();
  },
  data() {
    return {
      toggle_one: 0,
      isAnd: true,
      groups: [],
      rules: []
    };
  },
  methods: {
    clickAnd() {
      this.isAnd = true;
    },

    clickOr() {
      this.isAnd = false;
    },

    addRule() {
      var id = this.generateId();
      this.rules.push(id);
    },

    addGroup() {
      var id = this.generateId();
      this.groups.push(id);
    },

    deleteSelf() {
      this.$emit("delete-group");
    },

    deleteRule(index) {
      this.rules.splice(index, 1);
    },

    deleteGroup(index) {
      this.groups.splice(index, 1);
    },

    queryFormStatus() {
      var query = {};
      var rules = this.$refs.rules || {};
      var groups = this.$refs.groups || {};
      var i, j;

      query["condition"] = this.isAnd ? "AND" : "OR";
      query["rules"] = [];
      for (i = 0; i < rules.length; i++) {
        query.rules.push(rules[i].queryFormStatus());
      }
      for (j = 0; j < groups.length; j++) {
        query.rules[query.rules.length] = groups[j].queryFormStatus();
      }
      return query;
    },

    fillFormStatus(data) {
      var i, len;
      var group = this;
      group.rules = [];
      group.groups = [];
      if (data) {
        group.isAnd = /and/i.test(data.condition);
        len = data.rules.length;
        for (i = 0; i < len; i++) {
          if (data.rules[i].condition) {
            group.groups.push(group.generateId());
            (function(i, index) {
              group.$nextTick(function() {
                group.$refs.groups[index].fillFormStatus(data.rules[i]);
              });
            })(i, group.groups.length - 1);
          } else {
            group.rules.push(group.generateId());
            (function(i, index) {
              group.$nextTick(function() {
                group.$refs.rules[index].fillRuleStatus(data.rules[i]);
              });
            })(i, group.rules.length - 1);
          }
        }
      }
    },

    generateId() {
      return "xxxxxxxxxxxxxxxx".replace(/[xy]/g, function(c) {
        var r = (Math.random() * 16) | 0,
          v = c == "x" ? r : (r & 0x3) | 0x8;
        return v.toString(16);
      });
    }
  }
};
</script>

<style>
.and-or-template {
  padding: 8px;
  position: relative;
  border-radius: 3px;
  border: 1px solid #6d77b8;
  border-top: 3px solid #d2d6de;
  margin-bottom: 20px;
  /* width: 100%; */
  box-shadow: 0 1px 1px rgba(0, 0, 0, 0.1);
  border-top-color: #6d77b8;
  background-color: rgba(255, 255, 255, 0.9);
}

.and-or-template:before,
.and-or-template:after {
  content: "";
  position: absolute;
  left: -17px;
  width: 16px;
  height: calc(50% + 18px);
  border-color: #c0c5e2;
  border-style: solid;
}

.and-or-template:before {
  top: -18px;
  border-width: 0 0 2px 2px;
}

.and-or-template:after {
  top: 50%;
  border-width: 0 0 0 2px;
}

.and-or-first:before,
.and-or-first:after,
.and-or-template:last-child:after {
  border: none;
}

.and-or-top,
.btn-and-or {
  padding: 0;
}

.btn-and-or button {
  margin-left: 4px;
}

.and-or-offset {
  margin-left: 30px;
}
</style>