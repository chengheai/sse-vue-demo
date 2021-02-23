<template>
  <div class="video-wrapper">
    <video
      :autoplay="true"
      loop
      controls
      :src="url"
    />
    <!-- {{ messages }} -->
  </div>
</template>

<script>
import { mapActions, mapGetters } from 'vuex'
export default {
  data() {
    return {}
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
    const eventSource = new EventSource('http://localhost:4000/sse')
    eventSource.onmessage = function(event) {
      pushMessageAction(JSON.parse(event.data))
    }
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
