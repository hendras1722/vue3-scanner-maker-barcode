<template>
  <Writer
    value="wow2"
    :options="{ format: 'code128', lineColor: '#0aa', width: 54, height: 40 }"
  />

  <Scanner
    :track="paintBoundingBox"
    @detect="onDetect"
    @error="onError"
    :formats="['code_128', 'ean_8', 'upc_a', 'itf']"
  />
</template>

<script lang="ts" setup>
import Writer from "./components/Writer.vue";
import Scanner from "./components/Scanner.vue";
import { onMounted, ref } from "vue";

const result = ref<string>();
const error = ref({});

const constraints = ref({
  deviceId: "",
  facingMode: "user",
});

function GetDevice() {
  navigator.mediaDevices
    .enumerateDevices()
    .then(function (devices) {
      console.log(devices, "inidevices");
      devices.forEach(function (device) {
        if (device.label && device.label.includes("back")) {
          console.log(
            device.kind + ": " + device.label != undefined
              ? device.label
              : "Default"
          );
          constraints.value = {
            deviceId: device.deviceId,
            facingMode: "environtment",
          };
        }
      });
    })
    .catch(function (err) {
      console.log(err.name + ": " + err.message);
    });
}

onMounted(async () => {
  await GetDevice();
});

function paintBoundingBox(
  detectedCodes: Array<any>,
  ctx: CanvasRenderingContext2D
) {
  for (const detectedCode of detectedCodes) {
    const {
      boundingBox: { x, y, width, height },
    } = detectedCode;

    ctx.lineWidth = 2;
    ctx.strokeStyle = "#007bff";
    ctx.strokeRect(x, y, width, height);
  }
}

function onError(err: { name: string; message: string }) {
  error.value = `[${err.name}]: `;

  if (err.name === "NotAllowedError") {
    error.value += "you need to grant camera access permission";
  } else if (err.name === "NotFoundError") {
    error.value += "no camera on this device";
  } else if (err.name === "NotSupportedError") {
    error.value += "secure context required (HTTPS, localhost)";
  } else if (err.name === "NotReadableError") {
    error.value += "is the camera already in use?";
  } else if (err.name === "OverconstrainedError") {
    error.value += "installed cameras are not suitable";
  } else if (err.name === "StreamApiNotSupportedError") {
    error.value += "Stream API is not supported in this browser";
  } else if (err.name === "InsecureContextError") {
    error.value +=
      "Camera access is only permitted in secure context. Use HTTPS or localhost rather than HTTP.";
  } else {
    error.value += err.message;
  }
}

function onDetect(detectedCodes: Array<{ rawValue: string }>) {
  result.value = JSON.stringify(detectedCodes.map((code) => code.rawValue));
}
</script>
