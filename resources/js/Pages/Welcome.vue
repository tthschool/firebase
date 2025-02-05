<script setup>
import { ref, onMounted, watch } from 'vue';
import { initializeApp } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-app.js";
import { getDatabase, ref as dbRef, onValue, goOffline, goOnline  } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-database.js";
import { firebaseConfig } from "@/Const/firebaseconfig";
import Sample from '@/Pages/Sample.vue';
import { formatTime,playAudio } from '@/Const/firebaseconfig';


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
            if(data.error === false) {
                    for (let i = 0; i < 10; i++) {
                    setTimeout(() => {
                        playAudio();
                    }, i * 1000);
                }
            }

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

const reconnectFirebase = () => {
    isOffline.value = !isOffline.value
    if(isOffline.value) {
        goOffline(database);
    } else {
        goOnline(database);
    }
};

const isOffline = ref(false)
onMounted(getDevice);

</script>

<template>
    
    <Sample :device-name="deviceName" :product-name="productName" :error="error" :updated-time="updatedTime" :connection-status="connectionStatus" @connection-controller="reconnectFirebase" ></Sample>
</template>
