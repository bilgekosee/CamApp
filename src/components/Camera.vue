<script setup>
import { LineElement } from 'chart.js';
import {ref, onMounted} from 'vue';

const canvas = ref(null);
const video = ref(null);
const ctx = ref(null);

const constraints =ref ({
    audio:false,
    video:true
});

onMounted(async () => {
    if (video.value && canvas.value) {
        ctx.value= canvas.value.getContext("2d");
        await navigator.mediaDevices
        .getUserMedia(constraints.value) 
        .then(SetStream)
        .catch(e => {
            console.error(e);
        })
    }

})

function SetStream (stream) {
    video.value.srcObject =stream;
    video.value.play();
    requestAnimationFrame(Draw);
}
function Draw() {
    ctx.value.drawImage(video.value, 0, 0, canvas.value.width, canvas.value.height)
    requestAnimationFrame(Draw);
}
function TakePicture() {
    var link = document.createElement('a');
    link.download=`vue-cam-${new Date().toISOString()}.png`;
    link.href=canvas.value.toDataURL();
    link.click();
}
const countdown = ref(0);

function updateCountdown() {
  if (countdown.value > 0) {
    countdown.value--;
    setTimeout(updateCountdown, 1000); 
  }
}

function AfterSecond() {
  countdown.value = 10; 
  updateCountdown(); 

  setTimeout(function () {
    var link = document.createElement('a');
    link.download = `vue-cam-${new Date().toISOString()}.png`;
    link.href = canvas.value.toDataURL();
    link.click();
  }, 10000); 

</script>

<template>
    <div>
        <video ref="video" autoplay playsinline webkit-playsinline muted hidden></video>

        <canvas ref="canvas" width="1000" height="600" class="bg-black border-double border-8 border-sky-500 rounded-3xl"></canvas>

        <div class="flex items-center justify-center py-4">
            <button @click="TakePicture" class="px-6 py-4 text-justify-center h-14 bg-gradient-to-r from-sky-500 to-indigo-500 rounded text-white text-2xl uppercase font-bold
            hover:from-pink-500 hover:to-yellow-500">Take a Picture</button>
            <button @click="AfterSecond" class="px-6 py-4 mx-5 text-justify-center text-white  font-bold rounded bg-red-700 hover:bg-red-950">Take a photo after 10 seconds</button>
        </div>

    </div>
</template>
