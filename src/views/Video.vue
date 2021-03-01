<template>
  <div class="video-wrapper">
    <video
      id="my-video"
      autoplay
      loop
      :muted="true"
      controls
      :src="url"
    />
    <!-- <iframe
      allow="autoplay"
      style="border: none;"
      width="100%"
      height="600"
      title=""
      :src="url"
    /> -->
    <!-- {{ messages }} -->
  </div>
</template>

<script>
import { mapActions, mapGetters } from 'vuex'
export default {
  data() {
    return {
      isMuted: true
    }
  },
  computed: {
    ...mapGetters(['login', 'messages']),
    url() {
      return (
        this.messages[this.messages.length - 1].message || 'https://v-cdn.zjol.com.cn/276982.mp4'
      )
    }
  },
  created() {
    const pushMessageAction = this.pushMessageAction
    const eventSource = new EventSource(`${process.env.VUE_APP_BASE_API}/sse`)
    eventSource.onmessage = function(event) {
      pushMessageAction(JSON.parse(event.data))
    }
  },
  mounted() {
    // setTimeout(() => {
    //   this.isMuted = false
    //   let vdo = document.getElementById('my-video')
    //   vdo.play()
    // }, 500)
  },
  methods: {
    ...mapActions(['pushMessageAction'])
  }
}
</script>

<style scoped>
.video-wrapper {
	width: 100vw;
	height: 100vh;
	overflow: hidden;
}
video {
	width: 100%;
	height: 100%;
}
</style>
