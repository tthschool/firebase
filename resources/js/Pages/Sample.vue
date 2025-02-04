<script setup>
import { ref } from 'vue';
import { formatTime } from '@/Const/firebaseconfig';
const props = defineProps({
  error: Boolean,
  deviceName: String,
  productName: String,
  updatedTime: String,
  connectionStatus: Boolean
});

const time = ref()

function updateTime() {
  var cd = new Date();
  time.value = formatTime(cd)
};
const emit = defineEmits([
  'connectionController'
])

const connectionController = () => {
  emit('connectionController')
}

setInterval(updateTime, 1000);

</script>
<template>
  <div :class="error ? 'bg-blue-900' : 'bg-red-500'" class="w-screen h-screen p-6 flex flex-col justify-between text-white">
    <button @click="connectionController">toggle connection</button>
    <!-- <button @click="reconnectFirebase" v-if="isOffline">reconnect</button> -->
    <div class="flex justify-between items-center text-4xl font-bold">
      <p>{{ deviceName }}</p>
      <p>時刻　{{ time }}</p>
    </div>
    <div class="flex flex-col items-center justify-center flex-1">
      <div v-if="!error" class="text-white text-[30rem] font-bold">✖</div>
      <div v-else class="font-bold text-[30rem]">○</div>
    </div>
    <div class="flex flex-col items-center text-6xl">
      <p v-if="!error && connectionStatus" class="">商品：{{ productName }}</p>
      <p v-if="!connectionStatus" class="">connection error !!!</p>
      <p v-if="connectionStatus" class="font-bold">最終判定日時 : {{ updatedTime }}</p>
    </div>
   
  </div>
</template>