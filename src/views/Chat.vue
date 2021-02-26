<template>
  <div class="chat">
    <!-- <div class="messages">
			<div v-for="message in messages" :key="message.id" class="message">
				{{ message.user }}: {{ message.message }}
			</div>
		</div> -->

    <div class="controls">
      <el-input v-model="message" placeholder="请输入视频URL" />
      <el-button type="primary" style="margin-left:15px" @click="sendMessage">发 送</el-button>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src

import { mapActions, mapGetters } from 'vuex'

export default {
  name: 'Chat',
  data() {
    return {
      message: 'https://v-cdn.zjol.com.cn/276984.mp4'
    }
  },
  computed: mapGetters(['login', 'messages']),
  created() {
    const pushMessageAction = this.pushMessageAction
    const eventSource = new EventSource(`${process.env.VUE_APP_BASE_API}/sse`)
    eventSource.onmessage = function(event) {
      pushMessageAction(JSON.parse(event.data))
    }
    eventSource.onopen = function(event) {
      console.log(event)
    }

    eventSource.onerror = function(error) {
      console.log(error)
    }
  },
  methods: {
    sendMessage() {
      const notMP4 = this.message.split('.')[this.message.split('.').length - 1] !== 'mp4'
      if (notMP4) {
        this.$message.warning('mp4格式错误')
        return
      }
      fetch(`${process.env.VUE_APP_BASE_API}/send`, {
        headers: {
          'Content-Type': 'application/json'
        },
        method: 'POST',
        body: JSON.stringify({
          user: this.login,
          message: this.message
        })
      })
      this.message = ''
      this.$message.success('已发送')
    },
    ...mapActions(['pushMessageAction'])
  }


}
</script>

<style scoped>
.chat {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100vh;
}

.messages {
  flex: 1;
  padding: 10px;
  overflow: auto;
  display: flex;
  flex-direction: column;
}

.messages .message {
  padding: 10px;
  border-radius: 10px;
  background-color: blanchedalmond;
  margin: 10px;
}

.controls {
  width: 80%;
  display: flex;
}

.controls .message-input {
  flex: 1;
}

.controls > input {
  font-size: 20px;
}
</style>
