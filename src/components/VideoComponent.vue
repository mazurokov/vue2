<template>
  <div>
    <div style="max-width: 100%; overflow-x: auto">
      <pre v-if="mediaError">
        mediaError::: {{ mediaError }}
      </pre>
      <br>
      <pre v-if="videoErrorMessage">
        videoErrorMessage::: {{ videoErrorMessage }}
      </pre>
    </div>
    <div v-if="data?.withoutPlayback" class="without-playback">
      Відео недоступне для відтворення
    </div>
    <video
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
      mediaError: null,
      videoErrorMessage: ''
    }
  },
  mounted() {
    const video = this.$refs.videoPlayer;
    if (this.data?.file && !this.data?.withoutPlayback) {
      video.src = URL.createObjectURL(this.data.file);
    }
    video.addEventListener('error', () => {
      const error = video.error;
      if (error) {
        this.videoError = {
          code: error?.code,
          message: error?.message,
        };

        switch (error.code) {
          case MediaError.MEDIA_ERR_ABORTED:
            console.error('Відтворення відео перервано користувачем.');
            break;
          case MediaError.MEDIA_ERR_NETWORK:
            console.error('Помилка мережі під час завантаження відео.');
            break;
          case MediaError.MEDIA_ERR_DECODE:
            console.error('Помилка декодування відео.');
            this.mediaError = true;
            break;
          case MediaError.MEDIA_ERR_SRC_NOT_SUPPORTED:
            console.error('Формат відео або тип не підтримується.');
            this.mediaError = true;
            break;
          default:
            console.error('Невідома помилка відео.');
            this.mediaError = true;
            break;
        }
      }
    });


  }
}
</script>
