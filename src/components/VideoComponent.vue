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

    video.addEventListener('error', (event) => {
      const error = video.error;
      if (error) {
        console.error('Video error code:', error.code);

        switch (error.code) {
          case MediaError.MEDIA_ERR_ABORTED:
            console.error('Відтворення відео перервано користувачем.');
            break;
          case MediaError.MEDIA_ERR_NETWORK:
            console.error('Помилка мережі під час завантаження відео.');
            break;
          case MediaError.MEDIA_ERR_DECODE:
            console.error('Помилка декодування відео.');
            break;
          case MediaError.MEDIA_ERR_SRC_NOT_SUPPORTED:
            console.error('Формат відео або MIME-тип не підтримується.');
            break;
          default:
            console.error('Невідома помилка відео.');
            break;
        }
      }
    });

    if (this.data?.file && !this.data?.withoutPlayback) {
      video.src = URL.createObjectURL(this.data?.file);
    }
  },
};
</script>
