<template>
  <div>
    <input type="file" @change="openFile"/>
    <pre v-if="meta">{{ meta }}</pre>
  </div>
</template>

<script>
import MP4Box from 'mp4box';

export default {
  name: 'H265MetaReader',
  data() {
    return { meta: null };
  },
  methods: {
    openFile(e) {
      const file = e.target.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = ({ target }) => this.extractMeta(target.result);
        reader.readAsArrayBuffer(file);
      }
    },
    extractMeta(arrayBuffer) {
      const mp4boxfile = MP4Box.createFile();
      arrayBuffer.fileStart = 0;

      mp4boxfile.onReady = ({ tracks }) => {
        const vTrack = tracks.find(t => t.video);
        this.meta = { width: vTrack.track_width, height: vTrack.track_height };
      };
      mp4boxfile.appendBuffer(arrayBuffer);
    }
  }
};
</script>