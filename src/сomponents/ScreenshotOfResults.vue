<template>
  <div ref="shootingSpace" style="width: 100%; height: 100%">
    <img :src="template" style="width: 100%; height: 100%" />
  </div>
</template>

<script>
import template from "../templates/banana.png";
import html2canvas from "html2canvas";
import { Localstorage } from "../constants";
import { createPost } from "../services/userService";

export default {
  name: "screenshotOfResults",
  data() {
    return {
      template: template,
    };
  },
  methods: {
    screenshot() {
      return html2canvas(this.$refs.shootingSpace);
    },
    convertingCanvasToFile(canvas) {
      let arr = canvas.toDataURL().split(","),
        mime = arr[0].match(/:(.*?);/)[1],
        bstr = atob(arr[1]),
        n = bstr.length,
        u8arr = new Uint8Array(n);

      while (n--) {
        u8arr[n] = bstr.charCodeAt(n);
      }

      return new File([u8arr], "file", { type: mime });
    },
    async sendingImageToServer(file) {
      const accessToken = localStorage.getItem(Localstorage.ACCESS_TOKEN);
      await createPost(file, accessToken);
    },
  },
  async mounted() {
    const canvas = await this.screenshot();
    const file = await this.convertingCanvasToFile(canvas);
    await this.sendingImageToServer(file);
  },
};
</script>
