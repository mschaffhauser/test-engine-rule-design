<template>
<v-app>

    <v-container>
      <v-form ref="form"
    v-model="valid"
    lazy-validation>
       <conditions :options="options" :isFirst="isFirst" ref="andOr"></conditions>
  
      <div class="right">
        <v-btn color="error" @click.prevent="resetFilter">Reset Rules</v-btn>
        <v-btn color="success" @click="submitFilter">Submit Rules</v-btn>
      </div> 
          </v-form>
    </v-container>

  </v-app>
</template>

<script>
import Conditions from "@/components/Conditions";
import Modal from "@/components/Modal.vue";

export default {
  name: "app",
  components: {
    Conditions,
    Modal
  },
  data() {
    
    return {
        valid: true,
      options: {
        keys: [
          {
            name: "Choose Type",
            id: -99
          },
          {
            name: "Voucher Code",
            id: 134
          },
          {
            name: "Category Level 1",
            id: 87
          },
          {
            name: "Product SKU",
            id: 256
          },
          {
            name: "Brand",
            id: 121
          }
        ],

        operators: [
          {
            name: "Choose Conditions",
            value: -99
          },
          {
            name: "more",
            value: ">"
          },
          {
            name: "equal",
            value: "="
          },
          {
            name: "less",
            value: "<"
          },
          {
            name: "Is in",
            value: "Isin"
          }
        ]
      },

      isFirst: true,

      showQueryModal: false,

      showFillModal: false,

      fetchedQuery: "",

      filledQuery: ""
    };
  },

  mounted() {
    this.$nextTick(function() {
      this.init();
    });
  },

  methods: {
    init() {
      this.$refs.conditions.addRule();
      this.$refs.conditions.addGroup();
      this.$refs.conditions.addGroup();
    },

    fetchQuery() {
      this.showQueryModal = true;
      this.fetchedQuery = JSON.stringify(
        this.$refs.conditions.queryFormStatus(),
        null,
        4
      );
    },

    fillQuery() {
      var query = JSON.parse(this.filledQuery);
      this.$refs.conditions.fillFormStatus(query);
      this.showFillModal = false;
    },

    showModal() {
      this.showFillModal = true;
      this.filledQuery = "";
    },

    resetFilter() {
      this.$refs.conditions.groups = [];
      this.$refs.conditions.rules = [];
    }
  }
};
</script>