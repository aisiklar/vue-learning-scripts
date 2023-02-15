<script setup>
import { ref } from "vue";

const props = defineProps({
  greetingToChild: String,
  textToChild: String,
  countToChild: Number,
});

const emit = defineEmits(["responseFromChild"]);

const msgFromChild = ref("none yet");
const msgFromChildLog = ref("no message sent");

const clickMsgButton = () => {
  msgFromChildLog.value = "sent message";
  msgFromChild.value = "hi there, from child comp";
  emit("responseFromChild", msgFromChild.value);
};
</script>

<template>
  <div>
    <h1>this is child component</h1>
    <p>
      this is a greeting from parent component:
      {{ greetingToChild || null }}
    </p>
    <p>this is also from parent component: {{ textToChild || "none yet" }}</p>
    <p>Count from parent component: {{ countToChild || null }}</p>
    <div>
      <!-- <button
        @click="
          () => {
            msgFromchild = 'hi from childComp';
          }
        "
      >
        press to send your message
      </button> -->
      <button @click="clickMsgButton">Send your message</button>
      <p>{{ msgFromChildLog }}</p>
    </div>
  </div>
</template>

<style scoped></style>
