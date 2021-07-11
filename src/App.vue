<template>
  <app-form :id="1" />
  <app-form :id="2" />

  <alert ref="alertInstance" parentSelector="#app" />
</template>

<script lang="ts">
import { defineComponent } from "vue";

import Form from "./components/Form.vue";
import Alert, { getAlertInstance } from "./components/alert/Alert.vue";

export default defineComponent({
  components: {
    "alert": Alert,
    "app-form": Form,
  },
  setup() {
    const alertInstance = getAlertInstance();
    return {
      alertInstance,
    };
  },
});
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
