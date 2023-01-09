<template>
<v-container fluid grid-list-sm>
  <v-layout row wrap class="and-or-rule">
    <v-flex xs3>
      <v-select
        v-model="key"
        :items="options.keys"
        label="Type"
        return-object
        item-value="value"
        item-text="name"
        required
      ></v-select>
    </v-flex>

    <v-flex xs3>
      <v-select
        v-model="operator"
        :items="options.operators"
        label="Conditions"
        return-object
        item-value="value"
        item-text="name"
        required
      ></v-select>
    </v-flex>

    <v-flex xs3>
      <v-text-field required hide-details label="Value" type="text" v-model="value" placeholder="input"/>
    </v-flex>

    <v-btn fab icon @click.prevent="deleteSelf()">
      <v-icon>delete</v-icon>
    </v-btn>
  </v-layout>
  </v-container>
</template>

<script>
export default {
  name: "rule",
  props: ["options"],
  watch: {
    "options.keys.options"() {
      this.key = -99;
    },
    "options.conditions.options"() {
      this.condition = -99;
    }
  },
  data() {
    return {
      key: -99,
      operator: -99,
      value: ""
    };
  },
  methods: {
    deleteSelf() {
      this.$emit("delete-rule");
    },

    queryFormStatus() {
      return {
        key: this.key,
        operator: this.operator,
        value: this.value
      };
    },

    fillRuleStatus(data) {
      this.key = data.key;
      this.operator = data.operator;
      this.value = data.value;
    }
  }
};
</script>

<style>
.and-or-rule {
  position: relative;
  margin-left: 15px !important;
  padding-left: 17px;
}

.and-or-rule:before,
.and-or-rule:after {
  content: "";
  position: absolute;
  left: -1px;
  width: 16px;
  height: calc(50% + 15px);
  border-color: #c0c5e2;
  border-style: solid;
}

.and-or-rule:before {
  top: -15px;
  border-width: 0 0 2px 2px;
}

.and-or-rule:after {
  top: 50%;
  border-width: 0 0 0 2px;
}

.and-or-rule:last-child:after {
  border: none;
}
</style>