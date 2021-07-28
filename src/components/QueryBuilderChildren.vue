<template>
  <div class="vqb-children">
    <component
      :is="getComponent(child.type)"
      v-for="(child, index) in query.children"
      :key="index"
      :type="child.type"
      v-model:query="child.query"
      :rule-types="ruleTypes"
      :rules="rules"
      :rule="$parent.ruleById(child.query.rule)"
      :index="index"
      :max-depth="maxDepth"
      :depth="depth + 1"
      :labels="labels"
      @child-deletion-requested="$parent.removeChild"
      :groupComponent="groupComponent"
      :ruleComponent="ruleComponent"
    />
  </div>
</template>

<script>
export default {
  // eslint-disable-next-line vue/require-prop-types
  props: [
    "query",
    "ruleTypes",
    "rules",
    "maxDepth",
    "labels",
    "depth",
    "groupComponent",
    "ruleComponent",
  ],

  methods: {
    getComponent(type) {
      return type === "query-builder-group"
        ? this.groupComponent
        : this.ruleComponent;
    },
  },
};
</script>
