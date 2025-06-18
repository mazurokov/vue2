<template>
  <div>
    <div style="">
      <pre v-if="videoError">
      videoError::: {{videoError}}
    </pre>
      <br>
      <pre v-if="videoErrorMessage">
      videoErrorMessage::: {{videoErrorMessage}}
    </pre>
    </div>
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
      videoError: null,
      videoErrorMessage: '',
    };
  },
  mounted() {
    const video = this.$refs.videoPlayer;

    video.addEventListener('error', () => {
      const error = video.error;
      if (error) {
        this.videoError = {
          code: error.code,
          message: error.message,
        };
        switch (error.code) {
          case MediaError.MEDIA_ERR_ABORTED:
            this.videoErrorMessage = 'Відтворення відео перервано користувачем.';
            break;
          case MediaError.MEDIA_ERR_NETWORK:
            this.videoErrorMessage = 'Помилка мережі під час завантаження відео.';
            break;
          case MediaError.MEDIA_ERR_DECODE:
            this.videoErrorMessage = 'Помилка декодування відео.';
            break;
          case MediaError.MEDIA_ERR_SRC_NOT_SUPPORTED:
            this.videoErrorMessage = 'Формат відео або MIME-тип не підтримується.';
            break;
          default:
            this.videoErrorMessage = 'Невідома помилка відео.';
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
