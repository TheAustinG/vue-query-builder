<template>
  <div></div>
</template>

<script>
/* eslint-disable vue/require-default-prop */
import deepClone from "../utilities.js";
import QueryBuilderChildren from "./QueryBuilderChildren.vue";

export default {
  components: {
    // eslint-disable-next-line vue/no-unused-components
    QueryBuilderChildren,
  },

  props: {
    ruleTypes: Object,
    type: {
      type: String,
      default: "query-builder-group",
    },
    query: Object,
    rules: Array,
    index: Number,
    maxDepth: Number,
    depth: Number,
    labels: Object,
    groupComponent: Object,
    ruleComponent: Object,
  },

  data() {
    return {
      selectedRule: this.rules[0],
    };
  },
  watch: {
    rules: function () {
      if (this.rules) {
        this.selectedRule = this.rules[0];
      }
    },
  },
  computed: {
    selectedRuleId: function () {
      if (this.selectedRule) return this.selectedRule.id;
      else return null;
    },
  },

  methods: {
    ruleById(ruleId) {
      var rule = null;

      this.rules.forEach(function (value) {
        if (value.id === ruleId) {
          rule = value;
          return false;
        }
      });

      return rule;
    },

    addRule() {
      console.log(1);
      let updated_query = deepClone(this.query);
      console.log(2);
      let child = {
        type: "query-builder-rule",
        query: {
          rule: this.selectedRule.id,
          operator: this.selectedRule.operators[0],
          operand:
            typeof this.selectedRule.operands === "undefined"
              ? this.selectedRule.label
              : this.selectedRule.operands[0],
          value: null,
        },
      };
      console.log(3);
      // A bit hacky, but `v-model` on `select` requires an array.
      if (this.ruleById(child.query.rule).type === "multi-select") {
        console.log(4);
        child.query.value = [];
        console.log(5);
      }
      console.log(6);
      updated_query.children.push(child);
      console.log(7);
      this.$emit("update:query", updated_query);
      console.log(8);
    },

    addGroup() {
      let updated_query = deepClone(this.query);
      if (this.depth < this.maxDepth) {
        updated_query.children.push({
          type: "query-builder-group",
          query: {
            logicalOperator: this.labels.matchTypes[0].id,
            children: [],
          },
        });
        this.$emit("update:query", updated_query);
      }
    },

    remove() {
      this.$emit("child-deletion-requested", this.index);
    },

    removeChild(index) {
      let updated_query = deepClone(this.query);
      updated_query.children.splice(index, 1);
      this.$emit("update:query", updated_query);
    },
    updateRule: function (x) {
      const id = x.target.selectedOptions[0].value;
      this.selectedRule = this.ruleById(id);
    },
  },
};
</script>
