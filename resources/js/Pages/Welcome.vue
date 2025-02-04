<script setup>
import { ref, onMounted } from 'vue';
import { initializeApp } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-app.js";
import { getDatabase, ref as dbRef, onValue, goOffline, goOnline  } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-database.js";
import { firebaseConfig } from "@/Const/firebaseconfig";
import Sample from '@/Pages/Sample.vue';
import { formatTime } from '@/Const/firebaseconfig';


const props = defineProps({
    deviceNo: String
});

const app = initializeApp(firebaseConfig);
const database = getDatabase(app);

const deviceName = ref('')
const productName = ref('')
const updatedTime = ref('')
const error = ref('')
const connectionStatus = ref()
const getDevice = () => {
    const deviceRef = dbRef(database, `/${props.deviceNo}`);
    onValue(deviceRef, (snapshot) => {
        const data = snapshot.val();
        if (data) {
            deviceName.value = data.name
            productName.value = data.product_name
            error.value = data.error
            updatedTime.value  = formatTime(new Date(data.updated_time))
        }
    });
};

const connectedRef = dbRef(database, "/.info/connected");
onValue(connectedRef, (snapshot) => {
    if (snapshot.val() === true) {
        connectionStatus.value = true
    } else {
        connectionStatus.value = false
    }
});
const disconnectFirebase = () => {
    goOffline(database);
    isOffline.value = true;
};

// 🔹 Kết nối lại Firebase
const reconnectFirebase = () => {
    goOnline(database);
    isOffline.value = false;
};
const isOffline = ref(false)
onMounted(getDevice);
</script>

<template>
     <button @click="disconnectFirebase" v-if="!isOffline">🛑 Ngắt kết nối Firebase</button>
     <button @click="reconnectFirebase" v-if="isOffline">🔄 Kết nối lại Firebase</button>
    <Sample :device-name="deviceName" :product-name="productName" :error="error" :updated-time="updatedTime" :connection-status="connectionStatus" ></Sample>
</template>
