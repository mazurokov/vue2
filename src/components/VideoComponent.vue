<template>
  <div>
    <video
      ref="videoPlayer"
      controls
      @loadedmetadata="onLoaded"
      @error="onVideoError"
    ></video>

    <div v-if="isLoading">Завантажуємо відео…</div>
    <div v-if="errorMsg" class="error">{{ errorMsg }}</div>
  </div>
</template>

<script>
export default {
  name: 'VideoComponent',
  props: ['data'],
  data() {
    return {
      isLoading: true,
      errorMsg: null,
    };
  },

  methods: {
    onLoaded() {
      // відео готове до відтворення
      this.isLoading = false;
    },
    onVideoError(event) {
      this.isLoading = false;

      // video.error.code: 1–4 відповідно до стандарту HTMLMediaError
      const code = event.target.error?.code;
      switch (code) {
        case 1:
          this.errorMsg = 'Відмова браузера через політику безпеки.';
          break;
        case 2:
          this.errorMsg = 'Помилка завантаження файлу.';
          break;
        case 3:
          this.errorMsg = 'Пошкоджене медіа або невірний формат.';
          break;
        case 4:
          this.errorMsg = 'Формат не підтримується.';
          break;
        default:
          this.errorMsg = 'Невідома помилка відтворення відео.';
      }
    }
  },

  mounted() {
    const video = this.$refs.videoPlayer;
    if (this.data?.file?.name) {
      video.src = URL.createObjectURL(this.data.file);
    } else {
      this.isLoading = false;
      this.errorMsg = 'Файл не знайдено';
    }
  },
};
</script>
