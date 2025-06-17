<template>
  <div>
    <div v-if="data?.withoutPlayback" class="without-playback">
      Відео недоступне для відтворення
    </div>
    <video
      v-else
      ref="videoPlayer"
      crossorigin
      playsinline
      preload="none"
      controls
    >
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
    };
  },
  mounted() {
    const video = this.$refs.videoPlayer;
    if (this.data?.file && !this.data?.withoutPlayback) {
      video.src = URL.createObjectURL(this.data?.file);
    }
  },
};
</script>
