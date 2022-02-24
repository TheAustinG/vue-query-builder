<template>
  <!-- eslint-disable vue/no-v-html -->
  <div class="vqb-rule card">
    <div class="row gy-2 gx-3 align-items-center">
      

      <label class="col-auto">{{ rule.label }}</label>

      <div class="col-auto" v-if="typeof rule.operands !== 'undefined'">
        <!-- List of operands (optional) -->
        <select v-model="query.operand" class="form-select me-2">
          <option v-for="operand in rule.operands" :key="operand">
            {{ operand }}
          </option>
        </select>
      </div>

      <!-- List of operators (e.g. =, !=, >, <) -->
      <div
        class="col-auto"
        v-if="
          typeof rule.operators !== 'undefined' && rule.operators.length > 1
        "
      >
        <select v-model="query.operator" class="form-select me-2">
          <option
            v-for="operator in rule.operators"
            :key="operator"
            :value="operator"
          >
            {{ operator }}
          </option>
        </select>
      </div>

      <!-- Basic text input -->
      <div class="col-auto" v-if="rule.inputType === 'text'">
        <input
          v-model="query.value"
          class="form-control"
          type="text"
          :placeholder="labels.textInputPlaceholder"
        />
      </div>

      <!-- Basic number input -->
      <div class="col-auto" v-if="rule.inputType === 'number'">
        <input v-model="query.value" class="form-control" type="number" />
      </div>

      <!-- Datepicker -->
      <div class="col-auto" v-if="rule.inputType === 'date'">
        <input v-model="query.value" class="form-control" type="date" />
      </div>

      <!-- Custom component input -->
      <div v-if="isCustomComponent" class="col-auto vqb-custom-component-wrap">
        <component :is="rule.component" v-model="query.value" :rule="rule" />
      </div>

      <!-- Checkbox input -->
      <div v-if="rule.inputType === 'checkbox'" class="col-auto">
        <div
          v-for="choice in rule.choices"
          :key="choice.value"
          class="form-check form-check-inline"
        >
          <input
            :id="
              'depth' + depth + '-' + rule.id + '-' + index + '-' + choice.value
            "
            v-model="query.value"
            type="checkbox"
            :value="choice.value"
            class="form-check-input"
          />
          <label
            class="form-check-label"
            :for="
              'depth' + depth + '-' + rule.id + '-' + index + '-' + choice.value
            "
          >
            {{ choice.label }}
          </label>
        </div>
      </div>

      <!-- Radio input -->
      <div v-if="rule.inputType === 'radio'" class="col-auto">
        <div
          v-for="choice in rule.choices"
          :key="choice.value"
          class="form-check form-check-inline"
        >
          <input
            :id="
              'depth' + depth + '-' + rule.id + '-' + index + '-' + choice.value
            "
            v-model="query.value"
            :name="'depth' + depth + '-' + rule.id + '-' + index"
            type="radio"
            :value="choice.value"
            class="form-check-input"
          />
          <label
            class="form-check-label"
            :for="
              'depth' + depth + '-' + rule.id + '-' + index + '-' + choice.value
            "
          >
            {{ choice.label }}
          </label>
        </div>
      </div>

      <!-- Select without groups -->
      <div
        class="col-auto"
        v-if="rule.inputType === 'select' && !hasOptionGroups"
      >
        <select
          v-model="query.value"
          class="form-select"
          :multiple="rule.type === 'multi-select'"
        >
          <option
            v-for="option in selectOptions"
            :key="option.value"
            :value="option.value"
          >
            {{ option.label }}
          </option>
        </select>
      </div>

      <!-- Select with groups -->
      <div
        class="col-auto"
        v-if="rule.inputType === 'select' && hasOptionGroups"
      >
        <select
          v-model="query.value"
          class="form-select"
          :multiple="rule.type === 'multi-select'"
        >
          <optgroup
            v-for="(option, option_key) in selectOptions"
            :key="option_key"
            :label="option_key"
          >
            <option
              v-for="sub_option in option"
              :key="sub_option.value"
              :value="sub_option.value"
            >
              {{ sub_option.label }}
            </option>
          </optgroup>
        </select>
      </div>

      <!-- Remove rule button -->
      <div class="col-auto d-flex">
        <button
          type="button"
          class="close ms-auto btn"
          @click="remove"
          v-html="labels.removeRule"
        ></button>
      </div>
    </div>
  </div>
</template>

<script>
import QueryBuilderRule from "../../components/QueryBuilderRule";

export default {
  extends: QueryBuilderRule,
};
</script>
