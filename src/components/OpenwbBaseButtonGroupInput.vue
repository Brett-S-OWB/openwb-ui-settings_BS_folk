<template>
  <openwb-base-setting-element>
    <!-- TITLE -->
    <template #title>
      <slot name="title">{{ title }}</slot>
    </template>

    <!-- HELP -->
    <template
      v-if="$slots.help"
      #help
    >
      <slot name="help" />
    </template>

    <!-- BUTTON AREA -->
    <template #default>
      <!-- MULTI-ROW -->
      <div
        v-if="useMultiRow"
        class="btn-group-multi"
      >
        <openwb-base-button-row
          v-for="(row, index) in buttonRows"
          :key="index"
          :buttons="row"
          :uid="uid"
          :model-value="value"
          :disabled="disabled"
          @update:model-value="value = $event"
          @button-click="$emit('button-click', $event)"
        />
      </div>

      <!-- SINGLE ROW -->
      <openwb-base-button-row
        v-else
        :buttons="buttons"
        :uid="uid"
        :model-value="value"
        :disabled="disabled"
        @update:model-value="value = $event"
        @button-click="$emit('button-click', $event)"
      />
    </template>
  </openwb-base-setting-element>
</template>

<script>
import OpenwbBaseSettingElement from "./OpenwbBaseSettingElement.vue";
import OpenwbBaseButtonRow from "./OpenwbBaseButtonRow.vue";
import BaseSettingComponents from "./mixins/BaseSettingComponents.vue";

export default {
  name: "OpenwbButtonGroupInput",
  components: {
    OpenwbBaseSettingElement,
    OpenwbBaseButtonRow,
  },
  mixins: [BaseSettingComponents],
  inheritAttrs: false,
  props: {
    title: { type: String, required: false, default: "" },
    modelValue: { type: [String, Number, Boolean], default: undefined },
    buttons: { type: Array, required: true },
    disabled: { type: Boolean, default: false },
    maxPerRow: { type: Number, default: null },
  },
  emits: ["update:modelValue", "button-click"],
  computed: {
    value: {
      get() {
        return this.modelValue;
      },
      set(v) {
        this.$emit("update:modelValue", v);
      },
    },
    useMultiRow() {
      return this.maxPerRow && this.buttons.length > this.maxPerRow;
    },
    buttonRows() {
      if (!this.useMultiRow) return [this.buttons];
      const rows = [];
      for (let i = 0; i < this.buttons.length; i += this.maxPerRow) {
        rows.push(this.buttons.slice(i, i + this.maxPerRow));
      }
      return rows;
    },
  },
};
</script>

<style scoped>
/* Wrapper spans full width */
.btn-group-multi {
  width: 100%;
}

/* Remove visual gap between stacked rows */
.btn-group-multi .btn-group + .btn-group {
  margin-top: -1px; /* collapse the shared border */
}

/* First row – remove bottom corners */
.btn-group-multi :deep(.btn-group:first-child .btn:first-child) {
  border-bottom-left-radius: 0;
}

.btn-group-multi :deep(.btn-group:first-child .btn:last-child) {
  border-bottom-right-radius: 0;
}

/* Last row – remove top corners */
.btn-group-multi :deep(.btn-group:last-child .btn:first-child) {
  border-top-left-radius: 0;
}

.btn-group-multi :deep(.btn-group:last-child .btn:last-child) {
  border-top-right-radius: 0;
}
</style>
