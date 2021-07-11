<template>
  <form @submit.prevent="handleSubmit">
    Form {{ id }}
    <button type="submit">Submit</button>
  </form>
</template>

<script lang="ts">

import { defineComponent } from "vue";

import { getAlertInstance } from "./alert/Alert.vue";

export default defineComponent({
  props: {
    id: {
      type: Number,
      required: true,
    }
  },
  setup(props) {
    const alertInstance = getAlertInstance();

    const handleSubmit = async (): Promise<void> => {
      if (props.id === 1) {
        const alertResult1 = await alertInstance.alert({ title: "Do you agree?" });

        console.log(alertResult1);

        return;
      }

      const alertResult2 = await alertInstance.confirm({ title: "Do you confirm?" });
      console.log(alertResult2);
    };

    return {
      handleSubmit,
    };
  },
});
</script>

<style scoped>
form {
  padding: 5px;
  margin: 5px;
  border: 3px dashed lightblue;
}
</style>