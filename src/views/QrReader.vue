<template>
  <div>
    <div v-if="!isShowCamera">
      <p class="error">{{ error }}</p>

      <p class="decode-result">
        Resultado: <b>{{ result }}</b>
      </p>
    </div>
      <button @click="scanQrCode">{{!isShowCamera?'Scan qr code':'Exit QR reader'}}</button>

    <qrcode-stream v-if="isShowCamera" @decode="onDecode" @init="onInit" />
  </div>
</template>

<script>
export default {
  data() {
    return {
      result: "",
      error: "",
      isShowCamera: false,
    };
  },

  methods: {
    onDecode(result) {
      this.result = result;
      this.isShowCamera = false;
    },
    scanQrCode() {
      this.isShowCamera = !this.isShowCamera;
    },

    async onInit(promise) {
      try {
        await promise;
      } catch (error) {
        if (error.name === "NotAllowedError") {
          this.error = "ERROR: you need to grant camera access permisson";
        } else if (error.name === "NotFoundError") {
          this.error = "ERROR: no camera on this device";
        } else if (error.name === "NotSupportedError") {
          this.error = "ERROR: secure context required (HTTPS, localhost)";
        } else if (error.name === "NotReadableError") {
          this.error = "ERROR: is the camera already in use?";
        } else if (error.name === "OverconstrainedError") {
          this.error = "ERROR: installed cameras are not suitable";
        } else if (error.name === "StreamApiNotSupportedError") {
          this.error = "ERROR: Stream API is not supported in this browser";
        }
      }
    },
  },
};
</script>

<style scoped>
.error {
  font-weight: bold;
  color: red;
}
</style>