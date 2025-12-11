<template>
  <openwb-base-setting-element>
    <template #title>
      <slot name="title">
        {{ title }}
      </slot>
    </template>
    <template
      v-if="$slots.help"
      #help
    >
      <slot name="help" />
    </template>
    <template #default>
      <!-- MULTI-ROW VERSION (only if maxPerRow is set and buttons.length > maxPerRow) -->
      <div
        v-if="useMultiRow"
        class="btn-group-multi"
      >
        <div
          v-for="(row, rowIndex) in buttonRows"
          :key="rowIndex"
          class="btn-group btn-block btn-group-toggle"
        >
          <label
            v-for="button in row"
            :key="button.value"
            class="btn btn-same-size btn-centered"
            :disabled="disabled"
            :class="[
              { active: value == button.buttonValue },
              { disabled: disabled },
              button.class ? button.class : 'btn-outline-info',
            ]"
            :for="`${uid}-${button.buttonValue}`"
          >
            <span>
              <input
                :id="`${uid}-${button.buttonValue}`"
                v-model="value"
                type="radio"
                :value="button.buttonValue"
                v-bind="$attrs"
                :disabled="disabled"
                @click="$emit('button-click', button.buttonValue)"
              />
              <slot :name="'label-' + button.buttonValue">
                {{ button.text }}
              </slot>
              <span>&nbsp;</span>
              <font-awesome-icon
                :icon="['fas', 'check']"
                :style="[value == button.buttonValue ? 'visibility: visible' : 'visibility: hidden']"
              />
            </span>
          </label>
        </div>
      </div>
      <!-- Single row button group -->
      <div
        v-else
        class="btn-group btn-block btn-group-toggle"
      >
        <label
          v-for="button in buttons"
          :key="button.value"
          class="btn btn-same-size btn-centered"
          :disabled="disabled"
          :class="[
            { active: value == button.buttonValue },
            { disabled: disabled },
            button.class ? button.class : 'btn-outline-info',
          ]"
          :for="`${uid}-${button.buttonValue}`"
        >
          <span>
            <input
              :id="`${uid}-${button.buttonValue}`"
              v-model="value"
              type="radio"
              :value="button.buttonValue"
              v-bind="$attrs"
              :disabled="disabled"
              @click="$emit('button-click', button.buttonValue)"
            />
            <slot :name="'label-' + button.buttonValue">
              {{ button.text }}
            </slot>
            <span>&nbsp;</span>
            <font-awesome-icon
              :icon="['fas', 'check']"
              :style="[value == button.buttonValue ? 'visibility: visible' : 'visibility: hidden']"
            />
          </span>
        </label>
      </div>
    </template>
  </openwb-base-setting-element>
</template>

<script>
import OpenwbBaseSettingElement from "./OpenwbBaseSettingElement.vue";
import BaseSettingComponents from "./mixins/BaseSettingComponents.vue";

import { library } from "@fortawesome/fontawesome-svg-core";
import { faCheck as fasCheck } from "@fortawesome/free-solid-svg-icons";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";

library.add(fasCheck);

export default {
  name: "OpenwbButtonGroupInput",
  components: {
    FontAwesomeIcon,
    OpenwbBaseSettingElement,
  },
  mixins: [BaseSettingComponents],
  inheritAttrs: false,
  props: {
    title: { type: String, required: false, default: "" },
    modelValue: { type: [String, Number, Boolean], default: undefined },
    buttons: { type: Array, required: true },
    disabled: { type: Boolean, required: false, default: false },
    maxPerRow: { type: Number, default: null },
  },
  emits: ["update:modelValue", "button-click"],
  computed: {
    value: {
      get() {
        return this.modelValue;
      },
      set(newValue) {
        this.$emit("update:modelValue", newValue);
      },
    },
    useMultiRow() {
      return this.maxPerRow && this.buttons && this.buttons.length > this.maxPerRow;
    },
    buttonRows() {
      if (!this.useMultiRow) return [this.buttons];

      const rows = [];
      const perRow = this.maxPerRow;

      for (let i = 0; i < this.buttons.length; i += perRow) {
        rows.push(this.buttons.slice(i, i + perRow));
      }
      return rows;
    },
  },
};
</script>

<style scoped>
.btn.btn-centered {
  display: flex !important;
  align-items: center !important;
  justify-content: center !important;
}

.btn.btn-same-size {
  flex-basis: 10px !important; /* make buttons the same size */
}

/* --- Multi-row specific tweaks --- */
.btn-group-multi {
  width: 100%;
}

/* Remove visual gap between stacked rows */
.btn-group-multi .btn-group + .btn-group {
  margin-top: -1px; /* collapse the shared border */
}

/* Fix rounded corners so outer border looks like one big group (2 rows) */
.btn-group-multi .btn-group:first-child .btn:first-child {
  border-bottom-left-radius: 0;
}
.btn-group-multi .btn-group:first-child .btn:last-child {
  border-bottom-right-radius: 0;
}
.btn-group-multi .btn-group:last-child .btn:first-child {
  border-top-left-radius: 0;
}
.btn-group-multi .btn-group:last-child .btn:last-child {
  border-top-right-radius: 0;
}
</style>
