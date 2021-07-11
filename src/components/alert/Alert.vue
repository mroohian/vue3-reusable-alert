<template>
  <teleport :to="parentSelector">
    <transition name="modal">
      <div class="backdrop" v-if="isOpened">
        <div class="modal">
          <div>{{ title }}</div>
          <div class="actions">
            <button @click="close('true')">I Agree</button>
            <button @click="close('false')">I Decline</button>
          </div>
        </div>
      </div>
    </transition>
  </teleport>
</template>

<script lang="ts">
import { defineComponent, readonly, ref, Ref } from "vue";

enum ModalMode {
  Alert,
  Confirm,
  Prompt,
}

interface AlertParams {
  title: string;
}

interface ConfirmParams {
  title: string;
}

interface AlertResult {
  value: string;
}

export interface Alert {
  alert: (params: AlertParams) => Promise<AlertResult>;
  confirm: (params: ConfirmParams) => Promise<AlertResult>;
}

type AlertInstance = Ref<Alert | undefined> & Alert;

export default defineComponent({
  props: {
    parentSelector: {
      type: String,
      default: "body",
    },
  },
  setup() {
    const isOpened = ref<boolean>(false);
    const title = ref<string>("");
    const modalMode = ref<ModalMode>(ModalMode.Alert);

    let closeResolveFn: (value: AlertResult | PromiseLike<AlertResult>) => void = () => {
      /* Ignore by default */
    };

    const open = (): Promise<AlertResult> => {
      isOpened.value = true;

      return new Promise((resolve) => {
        closeResolveFn = resolve;
      });
    };

    const close = (value: AlertResult): void => {
      isOpened.value = false;
      closeResolveFn(value);
    };

    function alert(params: AlertParams) {
      if (modalMode.value !== ModalMode.Alert) {
        modalMode.value = ModalMode.Alert;
      }
      title.value = params.title;
      return open();
    }

    function confirm(params: ConfirmParams) {
      if (modalMode.value !== ModalMode.Confirm) {
        modalMode.value = ModalMode.Confirm;
      }
      title.value = params.title;
      return open();
    }

    const alertRef: Alert = {
      alert,
      confirm,
    };

    return {
      modalMode,
      title,

      isOpened: readonly(isOpened),
      open,
      close,

      ...alertRef,
    };
  },
});

function makeAlertInstance(): AlertInstance {
  const alertRef = ref<Alert>();

  return {
    ...alertRef,
    alert(params: AlertParams) {
      if (!this.value) {
        throw new Error("Alert dom element reference is missing");
      }
      return this.value.alert(params);
    },
    confirm(params: ConfirmParams) {
      if (!this.value) {
        throw new Error("Alert dom element reference is missing");
      }
      return this.value.confirm(params);
    },
  };
}

const INSTANCE: AlertInstance = makeAlertInstance();

export function getAlertInstance(): AlertInstance {
  return INSTANCE;
}
</script>

<style scoped>
.backdrop {
  display: block;
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  background-color: rgba(0, 0, 0, 0.5);
}

.modal {
  padding: 20px;
  position: absolute;
  width: 250px;
  height: 100px;
  border: 1px solid black;
  background-color: lightgray;
  left: calc(50% - 125px);
  top: calc(50% - 100px);
}
</style>