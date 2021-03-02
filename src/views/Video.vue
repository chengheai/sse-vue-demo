<template>
  <div class="video-wrapper">
    <el-row class="title">
      <el-col :span="12" class="name">{{ currentMsg.name }}</el-col>
      <el-col :span="12" class="image">
        <el-image :src="currentMsg.pic_url" fit="cover">
          <div slot="error" class="image-slot">
            <i class="el-icon-picture-outline" />
          </div>
        </el-image>
      </el-col>
    </el-row>
    <el-row class="video">
      <video-player
        ref="videoPlayer"
        class="video-player vjs-custom-skin"
        :playsinline="true"
        :options="playerOptions"
      />
    </el-row>
  </div>
</template>

<script>
import { mapActions, mapGetters } from 'vuex'
import liu from './../assets/liu.jpeg'
export default {
  data() {
    return {
      ruleForm: {
        name: '刘亦菲',
        pic_url: liu,
        video_url: 'https://v-cdn.zjol.com.cn/276982.mp4'
      },
      playerOptions: {
        playbackRates: [0.5, 1.0, 1.5, 2.0], // 可选的播放速度
        autoplay: true, // 如果为true,浏览器准备好时开始回放。
        muted: true, // 默认情况下将会消除任何音频。
        loop: true, // 是否视频一结束就重新开始。
        preload: 'auto', // 建议浏览器在<video>加载元素后是否应该开始下载视频数据。auto浏览器选择最佳行为,立即开始加载视频（如果浏览器支持）
        language: 'zh-CN',
        aspectRatio: '16:9', // 将播放器置于流畅模式，并在计算播放器的动态大小时使用该值。值应该代表一个比例 - 用冒号分隔的两个数字（例如"16:9"或"4:3"）
        fluid: true, // 当true时，Video.js player将拥有流体大小。换句话说，它将按比例缩放以适应其容器。
        sources: [{
          type: 'video/mp4', // 类型
          src: '' // url地址
        }],
        poster: '', // 封面地址
        notSupportedMessage: '此视频暂无法播放，请稍后再试', // 允许覆盖Video.js无法播放媒体源时显示的默认信息。
        controlBar: {
          timeDivider: true, // 当前时间和持续时间的分隔符
          durationDisplay: true, // 显示持续时间
          remainingTimeDisplay: false, // 是否显示剩余时间功能
          fullscreenToggle: true // 是否显示全屏按钮
        }
      }

    }
  },
  computed: {
    ...mapGetters(['login', 'messages']),
    myPlayer() {
      return this.$refs.videoPlayer.player
    },
    currentMsg() {
      let temp = this.messages[this.messages.length - 1].message
      return typeof (temp) == 'string' ? this.ruleForm : temp
    },
    video_url() {
      const { video_url } = this.messages[this.messages.length - 1].message
      return video_url || this.ruleForm.video_url
    }
  },
  watch: {
    currentMsg: {
      handler(newVal, oldVal) {
        this.fetchUrl()
      },
      deep: true,
      immediate: true
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
    this.fetchUrl()
  },
  methods: {
    ...mapActions(['pushMessageAction']),
    fetchUrl() {
      this.$nextTick(() => {
        this.playerOptions.sources[0].src = this.video_url
        // console.log(this.player)
        // this.myPlayer.play()
        // this.playerOptions.muted = false
      })
    }
  }
}
</script>

<style scoped>
.video-wrapper {
	width: 100vw;
	height: 100vh;
  padding: 10px 20px;
  box-sizing: border-box;
  background: url('./../assets/bg.jpg') no-repeat;
	overflow: hidden;
  display: flex;
  flex-direction: column;
  justify-content: space-around;
}
.title{
  height: 30%;
  display: flex;
}
.name{
  font-size: 5em;
  color: #fff;
  height: 100%;

  display: flex;
  justify-content: center;
  align-items: center;
}
.image{
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}
.el-image{
  height: 200px;
  width: 200px;
  /* border: 2px solid #fff; */
}
.video {
	height: 60%;
  display: flex;
  justify-content: center;
  align-items: center;
}
.video-player {
	width: 55%;
	height: 100%;
}
</style>
