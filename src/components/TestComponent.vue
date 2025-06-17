<template>
  <div>
    <input type="file" @change="openFile"/>
    <pre v-if="videoData.length">{{ videoData }}</pre>
  </div>

  <div class="videos-container">
    <VideoComponent
      v-for="(item, index) of videoData"
      :key="index"
      :data="item"
    ></VideoComponent>
  </div>
</template>

<script>
import MP4Box from 'mp4box';
import VideoComponent from '@/components/VideoComponent.vue'

export default {
  name: 'H265MetaReader',
  components: { VideoComponent },
  data() {
    return {
      meta: null,
      videoUrl: null,
      videoData: [],
    };
  },
  methods: {
    openFile(e) {
      const file = e.target.files[0];
      if (file) {

        const reader = new FileReader();
        reader.onload = async ({ target }) => {
          try {
            const meta = await this.extractMeta(target.result);
            this.meta = meta;
            const type = file.name.split('.').pop().toLowerCase()
            let withoutPlayback = false;
            if(meta.codec?.includes('hvc1')
              || meta.codec?.includes('hev1')
              || type !== 'mp4'){
              withoutPlayback = true;
            }
            const video = {
              meta,
              file,
              type,
              withoutPlayback,
            }
            this.videoData.push(video);

          } catch (error) {
            console.error("Error reading metadata:", error);
          }
        };
        reader.readAsArrayBuffer(file);
      }
    },
    extractMeta(arrayBuffer) {
      return new Promise((resolve, reject) => {
        // Копіюємо ArrayBuffer, щоб не змінювати його безпосередньо
        const copiedArrayBuffer = arrayBuffer.slice(0);

        const mp4boxfile = MP4Box.createFile();

        // Тепер змінюємо копію, а не оригінальний arrayBuffer
        copiedArrayBuffer.fileStart = 0;

        mp4boxfile.onReady = ({ tracks }) => {
          const vTrack = tracks.find(t => t.video);
          if (vTrack) {
            resolve({
              width: vTrack.track_width,
              height: vTrack.track_height,
              codec: vTrack.codec,
            });
          } else {
            reject(new Error('No video track found'));
          }
        };

        mp4boxfile.onError = (error) => reject(new Error(error));

        mp4boxfile.appendBuffer(copiedArrayBuffer);
      });
    }
  },

};
</script>
<style lang="sass">
.videos-container
  display: flex
  gap: 16px
  flex-wrap: wrap

  >*
    position: relative
    width: 300px
    aspect-ratio: 1/1

    video
      width: 100%
      height: auto
      max-height: 100%
      object-fit: cover
</style>