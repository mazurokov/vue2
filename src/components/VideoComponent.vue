<template>
  <div>
    <video
      ref="videoPlayer"
      crossorigin
      playsinline
      preload="none"
      controls
    >
      <source v-if="data?.type === 'mov'" :src="videoUrl" type="video/quicktime">
      <source v-if="data?.type === 'mp4'" :src="videoUrl" type="video/mp4">
    </video>
  </div>
</template>

<script>
export default {
  name: 'VideoComponent',
  props: ['data'],
  data() {
    return {
      meta: null,
      videoUrl: null,
    };
  },
  mounted() {
    const video = this.$refs.videoPlayer;
    if (this.data?.file) {
      video.src = URL.createObjectURL(this.data?.file);
      video.load();
      this.videoUrl = video.src;
    }
  },
};
</script>
