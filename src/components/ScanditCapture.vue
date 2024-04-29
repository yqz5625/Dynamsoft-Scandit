<script setup lang="ts">
import { onMounted, onUnmounted, ref, type Ref } from "vue";
import * as SDCCore from "scandit-web-datacapture-core";
import * as SDCBarcode from "scandit-web-datacapture-barcode";
import {
  barcodeCaptureLoader,
  BarcodeCapture,
  BarcodeCaptureSettings,
  Symbology,
  BarcodeCaptureOverlay,
  BarcodeCaptureOverlayStyle,
  SymbologyDescription,
} from "scandit-web-datacapture-barcode";
import type {
  SymbologySettings,
  Barcode,
  BarcodeCaptureSession,
} from "scandit-web-datacapture-barcode";
import {
  DataCaptureView,
  Camera,
  DataCaptureContext,
  configure,
  SingleImageUploader,
  CameraSwitchControl,
  RectangularViewfinder,
  RectangularViewfinderStyle,
  RectangularViewfinderLineStyle,
  FrameSourceState,
} from "scandit-web-datacapture-core";
const text: any = ref ("dw");
const type: any = ref("null")

onMounted(async () => {
  //   const view = new SDCCore.DataCaptureView();
  //   view.connectToElement(document.getElementById("uiContainer"));
  //   view.showProgressBar();
  //   view.setProgressBarMessage("Loading ...");

  await SDCCore.configure({
    licenseKey:
      "AgwUIiLmBl9MFixs4wEsB7UdpWJKRNo1GBs4i8oOSuAXQBA003oVZyJADR7VIBgmLFLO4eZUM+H5Yk/Eq3rW7BcXwBn9bJ+aIHod7+duJbe3QWfAaSuZddg0qcpsU54T6nYoOt5ZEtqSR/vU5HUzBmxMzPFTWeZYvmExSExYnlp7Ss1V7VicgvN6UM9SY9WDvUDSYvNg5fNpeDPFFmN/Y6sMmtmpVNpazmPHR0hFWjRZQaMpPExVUJZRHK9GWC06YA5SERRD7MukX3/te3m8ZH5KwRzXRL+EHV+XdsJrbPgfbswqL3Z2oPVRLnd2bOkJgmwjqWBJJtSlElYXdw9gZB1gR2AJTVuXkGZZGy9PyQxReWMhGxAqY69vzYE3aPH8BjVhC7tAlQ2xWyFsgncFRrYkZQkSXlo+CEzi7A436luUaSXcTnyg6jhNtXGVXsPkFnfCFap3m95LWKx47ydHNKo55KiKd5AjnViF+qVHUsDKeiyZKnxNxWxgmA9dahMfAGH3LZEmV3MTSMGcnXOxjFkdOE+6G6Z2AFeEk64Gg78IMM3dfhTJ5OVGGXK2D34TskHzYwRQr2bD4ksNhdV1uUSo1EszU3Tw90jh5KDUmezo94tll6yoYEgLzX/YCrLDdcTYAAPXF/I6oOvmORasrqEO3wyNnTK7MvGvqVqEH8cCygvD1kEBLucS22wzdxJpKxF/enurPTO/TUp1VAXHXWiyzZnj1Mg7cK3hpODqGZ5H4sSlHKz2gNeCcUkaxZngE+RN02aDk56Yz1mZxruGbMtw+FqJxCn5qC2yBKSxqjypcLGaNIeqLYa7azR+1GyxRVqVSw143YRtk/gOcr55lDavm/WFkRtAzeK6y+4ngZUXvFLhqB69PTSFfXvgtUflsE9HyuiOxQajL5D6xvWA+ZDTHwk6SlSIlZBOpAtpe8RXwMGLjeVhbVvNotvMahqNipGTapd4xG76Pup2OM25Db+CJoEdF3FXrKQy+S6gZOGR2WHxSvjwxZ/kSxDPv6s7eXmemYu/zcLxn0uDQgUeD6WNbgC6JAU+NHx/L1NeuAJx1Z9OpMqS5DZgHs76cqZTxQTuNQFSmu2WXkH5tM3y9HrNRKXLQWPh9aO9kaUBeW1ls4/Odpnd3sD5c0gToBOU6sDYGW1CcaVqv9qTUlR5nGfZqLihfaPB/80lu6VtO1EtPEoyQ1VHouNDUJYZmK5EX9OMdBqQFLun01atGURvFlvE",
    libraryLocation:
      "https://cdn.jsdelivr.net/npm/scandit-web-datacapture-barcode@6.x/build/engine/, https://unpkg.com/browse/scandit-web-datacapture-barcode@6.x/build/engine/",
    moduleLoaders: [barcodeCaptureLoader()],
  });

  // load the framework
  await SDCCore.configure({
    licenseKey: "-- ENTER YOUR SCANDIT LICENSE KEY HERE --",
    libraryLocation: new URL("library/engine/", document.baseURI).toString(),
    moduleLoaders: [SDCBarcode.barcodeCaptureLoader()],
  });

  // create the data capture context.
  const context: SDCCore.DataCaptureContext =
    await SDCCore.DataCaptureContext.create();

  // create a barcode capture mode with one symbology enabled
  const settings: SDCBarcode.BarcodeCaptureSettings =
    new SDCBarcode.BarcodeCaptureSettings();
  settings.enableSymbologies([
    SDCBarcode.Symbology.Code128,
    SDCBarcode.Symbology.Code39,
    SDCBarcode.Symbology.QR,
    SDCBarcode.Symbology.EAN8,
    SDCBarcode.Symbology.UPCE,
    SDCBarcode.Symbology.EAN13UPCA,
  ]);
  const barcodeCapture = await SDCBarcode.BarcodeCapture.forContext(
    context,
    settings
  );

  // initialize a listener
  barcodeCapture.addListener({
    didUpdateSession: (
      barcodeCaptureMode: SDCBarcode.BarcodeCapture,
      session: SDCBarcode.BarcodeCaptureSession
    ) => {
        console.log("test===================")
      if (session.newlyRecognizedBarcodes.length > 0) {
        // display a result in your custom view
        // ...
        text.value = session.newlyRecognizedBarcodes[0].data
        const symbology: SymbologyDescription = new SymbologyDescription(text.value.symbology)
        type.value = symbology.readableName
      } else {
        // inform the user there was no result
        // ...
      }
    },
    // didScan: (
    //   barcodeCaptureMode: SDCBarcode.BarcodeCapture,
    //   session: SDCBarcode.BarcodeCaptureSession
    // ) => {
    //   console.log(session.newlyRecognizedBarcodes);

    //   if (session.newlyRecognizedBarcodes.length > 0) {
    //     // display a result in your custom view
    //     // ...
    //     console.log(session.newlyRecognizedBarcodes);
    //   } else {
    //     // inform the user there was no result
    //     // ...
    //   }
    // },
  });

  // whenever the user has selected a file, process it.
  const fileInput: any = document.getElementById("fileInput");
  fileInput.addEventListener("change", async (event: any) => {
    let imageFileFrameSource: any;
    console.log("test1")
    // disable the button while processing the frame
    try {
      for (let i = 0; i < event.target.files.length; i++)
        imageFileFrameSource = await SDCCore.ImageFrameSource.fromFile(
          event.target.files[i]
        );
      await context.setFrameSource(imageFileFrameSource);
    } catch (error) {
      // can happen if the file is not a valid image.
      // Inform the user, reset the input.
      // ...
    }
    // process the file by turning on the frame source
    await imageFileFrameSource.switchToDesiredState(
      SDCCore.FrameSourceState.On
    );

    // reset the input
    fileInput.disabled = false;
    fileInput.value = "";
  });
});

onUnmounted(async () => {});
</script>

<template>
  <!-- <div id="uiContainer"></div> -->
  <!-- <div class="img-ipt"><input type="file" ref="iptRef" @change="useFile" /></div> -->
  <label
    >Select or take a picture:
    <input id="fileInput" type="file" accept="image/*" multiple/>
  </label>

  <div>result:</div>
  <span>"Text": {{ text }}</span>
  
</template>

<style scoped>
.capture-img {
  width: 100%;
  height: 100%;
  font-family: Consolas, Monaco, Lucida Console, Liberation Mono,
    DejaVu Sans Mono, Bitstream Vera Sans Mono, Courier New, monospace;
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
