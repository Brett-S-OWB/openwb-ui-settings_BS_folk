<template>
  <div class="btn-group btn-block btn-group-toggle">
    <label
      v-for="button in buttons"
      :key="button.buttonValue"
      class="btn btn-same-size btn-centered"
      :class="[
        { active: modelValue === button.buttonValue },
        { disabled: disabled },
        button.class ?? 'btn-outline-info',
      ]"
      :disabled="disabled"
      :for="`${uid}-${button.buttonValue}`"
    >
      <span>
        <input
          :id="`${uid}-${button.buttonValue}`"
          v-model="localValue"
          type="radio"
          :value="button.buttonValue"
          :disabled="disabled"
          @click="$emit('button-click', button.buttonValue)"
        />
        <slot :name="'label-' + button.buttonValue">
          {{ button.text }}
        </slot>
        <span>&nbsp;</span>
        <font-awesome-icon
          :icon="['fas', 'check']"
          :style="{ visibility: modelValue === button.buttonValue ? 'visible' : 'hidden' }"
        />
      </span>
    </label>
  </div>
</template>

<script>
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";

export default {
  name: "OpenwbButtonRow",
  components: { FontAwesomeIcon },
  props: {
    buttons: { type: Array, required: true },
    modelValue: { type: [String, Number, Boolean], default: null },
    disabled: { type: Boolean, default: false },
    uid: { type: String, required: true },
  },
  emits: ["update:modelValue", "button-click"],
  computed: {
    localValue: {
      get() {
        return this.modelValue;
      },
      set(value) {
        this.$emit("update:modelValue", value);
      },
    },
  },
};
</script>

<style scoped>
/* Flex center the contents */
.btn.btn-centered {
  display: flex !important;
  align-items: center !important;
  justify-content: center !important;
}

/* Uniform sizing */
.btn.btn-same-size {
  flex-basis: 10px !important;
}
</style>
