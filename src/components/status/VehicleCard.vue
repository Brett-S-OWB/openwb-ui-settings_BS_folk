<template>
  <status-card
    subtype="info"
    :component-id="vehicleIndex"
    :state="$store.state.mqtt[baseTopic + '/get/fault_state']"
    :state-message="$store.state.mqtt[baseTopic + '/get/fault_str']"
  >
    <template #header-left>
      <font-awesome-icon :icon="['fas', 'car']" />
      {{ vehicleName }}
    </template>
    <template
      v-if="soc != '-'"
      #header-right
    >
      {{ soc }}&nbsp;%
    </template>
    <!-- Fahrzeugdaten -->
    <openwb-base-card
      title="Fahrzeugdaten"
      subtype="white"
      body-bg="white"
      class="py-1 mb-2"
    >
      <div class="row">
        <div class="col pr-0 text-right">Ladestand</div>
        <div class="col pr-0 text-right">Reichweite</div>
        <div class="col pr-0 text-right">Letzter Zeitstempel</div>
      </div>
      <div class="row">
        <div class="col text-right text-monospace">{{ soc }}&nbsp;%</div>
        <div class="col text-right text-monospace">{{ socRange }}&nbsp;km</div>
        <div class="col text-right text-monospace">{{ socTimestamp }}</div>
      </div>
    </openwb-base-card>
  </status-card>
</template>

<script>
import ComponentState from "../mixins/ComponentState.vue";
import StatusCard from "./StatusCard.vue";

import { library } from "@fortawesome/fontawesome-svg-core";
import { faCar as fasCar } from "@fortawesome/free-solid-svg-icons";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";

library.add(fasCar);

export default {
  name: "VehicleCard",
  components: {
    StatusCard,
    FontAwesomeIcon,
  },
  mixins: [ComponentState],
  props: {
    vehicleKey: { type: String, required: true },
    vehicleName: { type: String, default: "" },
  },
  data() {
    return {
      mqttTopicsToSubscribe: ["openWB/vehicle/+/get/+"],
    };
  },
  computed: {
    vehicleIndex: {
      get() {
        return parseInt(this.vehicleKey.match(/(?:\/)(\d+)(?=\/)/)[1]);
      },
    },
    soc: {
      get() {
        return this.formatNumberTopic(this.baseTopic + "/get/soc");
      },
    },
    socTimestamp: {
      get() {
        if (this.$store.state.mqtt[this.baseTopic + "/get/soc_timestamp"] !== undefined) {
          return new Date(this.$store.state.mqtt[this.baseTopic + "/get/soc_timestamp"] * 1000).toLocaleString();
        } else {
          return "-";
        }
      },
    },
    socRange: {
      get() {
        if (this.$store.state.mqtt[this.baseTopic + "/get/range"] !== undefined) {
          return Math.round(this.$store.state.mqtt[this.baseTopic + "/get/range"]);
        } else {
          return 0;
        }
      },
    },
    baseTopic: {
      get() {
        return "openWB/vehicle/" + this.vehicleIndex;
      },
    },
  },
};
</script>
