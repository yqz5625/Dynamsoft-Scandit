<script setup lang="ts">
import { onMounted, onUnmounted, ref, type Ref } from "vue";
import * as SDCCore from "scandit-web-datacapture-core";
import { barcodeCaptureLoader } from "scandit-web-datacapture-barcode";
const view = new SDCCore.DataCaptureView();

view.connectToElement(document.getElementById("data-capture-view"));
view.showProgressBar();
view.setProgressBarMessage("Loading ...");

await SDCCore.configure({
  licenseKey: "-- ENTER YOUR SCANDIT LICENSE KEY HERE --",
  libraryLocation: new URL("library/engine/", document.baseURI).toString(),
  moduleLoaders: [barcodeCaptureLoader()],
});

view.hideProgressBar()

const context = await SDCCore.DataCaptureContext.create();
await view.setContext(context);

onMounted(() => {
})

onUnmounted(async () => {

})


</script>

<template>
    <div class="capture-img" id="data-capture-view">
        <div class="img-ipt"><input type="file" ref="iptRef" @change="captureImage" /></div>
        <div class="result-area" ref="resRef"></div>
    </div>
</template>
    
<style scoped>
.capture-img {
    width: 100%;
    height: 100%;
    font-family: Consolas, Monaco, Lucida Console, Liberation Mono, DejaVu Sans Mono, Bitstream Vera Sans Mono, Courier New, monospace;
}

.capture-img .img-ipt {
    width: 80%;
    height: 100%;
    display: flex;
    justify-content: center;
    border: 1px solid black;
    margin: 0 auto;
}

.capture-img .result-area {
    margin-top: 20px;
}
</style>