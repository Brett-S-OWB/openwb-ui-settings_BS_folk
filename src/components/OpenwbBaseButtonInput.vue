<template>
  <div class="form-row mb-1">
    <label
      class="col-md-4 col-form-label"
      @click="toggleHelp"
    >
      {{ title }}
      <font-awesome-icon
        v-if="$slots.help"
        :icon="
          showHelp
            ? ['fas', 'question-circle']
            : ['far', 'question-circle']
        "
        :class="showHelp ? 'text-info' : ''"
      />
    </label>
    <div class="col-md-8">
      <div class="form-row">
        <openwb-base-click-button
          :class="(disabled ? 'btn-outline-' : 'btn-') + subtype"
          :disabled="disabled"
          v-bind="$attrs"
          @button-clicked="handleClick"
        >
          <slot>{{ buttonText }}</slot>
        </openwb-base-click-button>
      </div>
      <span
        v-if="showHelp"
        class="form-row alert alert-info my-1 small"
      >
        <slot name="help" />
      </span>
    </div>
  </div>
</template>

<script>
import { library } from "@fortawesome/fontawesome-svg-core";
import { faQuestionCircle as fasQuestionCircle } from "@fortawesome/free-solid-svg-icons";
import { faQuestionCircle as farQuestionCircle } from "@fortawesome/free-regular-svg-icons";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";

library.add(fasQuestionCircle, farQuestionCircle);

export default {
  name: "OpenwbButtonInput",
  components: {
    FontAwesomeIcon,
  },
  inheritAttrs: false,
  props: {
    title: { type: String, required: false, default: "" },
    buttonText: { type: String, required: false, default: undefined },
    disabled: { type: Boolean, default: false },
    subtype: {
      type: String,
      validator: function (value) {
        return (
          [
            "info",
            "success",
            "warning",
            "danger",
            "primary",
            "secondary",
            "light",
            "dark",
          ].indexOf(value) !== -1
        );
      },
      default: "secondary",
    },
  },
  emits: ["buttonClicked"],
  data() {
    return {
      showHelp: false,
    };
  },
  methods: {
    toggleHelp() {
      this.showHelp = !this.showHelp && this.$slots.help !== undefined;
    },
    handleClick(event) {
      this.$emit("buttonClicked", event);
    },
  },
};
</script>
