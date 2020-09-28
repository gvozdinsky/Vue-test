<template>
  <div class="page">
    <video ref="video" class="video" autoplay playsinline></video>
    <div class="controls">
      <button v-on:click="toggleVideo()" class="button--green">
        Camera {{ isVideoPlaying ? 'OFF' : 'ON' }}
      </button>
    </div>

    <div class="comments">
      <h2>Comments:</h2>
      <div class="comment" v-for="comment in comments" :key="comment.id">
        <b>{{ comment.email }}:</b> {{ comment.body }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      stream: null,
      isVideoPlaying: true,
    }
  },
  methods: {
    async startVideo() {
      const constraints = {
        video: true,
      }
      try {
        this.stream = await navigator.mediaDevices.getUserMedia(constraints)
        this.$refs.video.srcObject = this.stream
        console.log('Stream:', this.stream)
      } catch (e) {
        console.error('Error accessing media devices.', e)
      }
    },
    stopVideo() {
      this.stream.getVideoTracks()[0].stop()
    },
    async toggleVideo() {
      if (this.isVideoPlaying) {
        this.stopVideo()
      } else {
        await this.startVideo()
      }
      this.isVideoPlaying = !this.isVideoPlaying
    },
  },
  async mounted() {
    await this.startVideo()
  },
  async asyncData() {
    const comments = await (
      await fetch('https://jsonplaceholder.typicode.com/comments')
    ).json()
    return {
      comments: comments,
    }
  },
}
</script>

<style scoped>
.page {
  max-width: 900px;
  margin: 0 auto;
}
.controls {
  text-align: center;
}
.video {
  min-height: 300px;
  max-height: 600px;
  width: 100%;
  pointer-events: none;
  margin: 0 auto;
}
.comments {
  margin: 60px auto;
}
.comment {
  margin-bottom: 16px;
}
</style>

