<template>
  <div class="chat">
    <!-- <div class="messages">
			<div v-for="message in messages" :key="message.id" class="message">
				{{ message.user }}: {{ message.message }}
			</div>
		</div> -->

    <!-- <div class="controls">
      <el-input v-model="message" placeholder="请输入视频URL" />
      <el-button type="primary" style="margin-left:15px" @click="sendMessage">发 送</el-button>
    </div> -->
    <el-form
      ref="ruleForm"
      :model="ruleForm"
      :rules="rules"
      label-suffix=":"
      label-width="100px"
      class="demo-ruleForm"
    >
      <el-form-item label="名字" prop="name">
        <el-input v-model="ruleForm.name" placeholder="请输入名字" />
      </el-form-item>
      <el-form-item label="图片地址" prop="pic_url">
        <el-input v-model="ruleForm.pic_url" placeholder="请输入图片URL" />
      </el-form-item>
      <el-form-item label="视频地址" prop="video_url">
        <el-input v-model="ruleForm.video_url" placeholder="请输入视频URL" />
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submitForm('ruleForm')">发 送</el-button>
        <el-button @click="resetForm('ruleForm')">重 置</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
// @ is an alias to /src

import { mapActions, mapGetters } from 'vuex'

export default {
  name: 'Chat',
  data() {
    return {
      ruleForm: {
        name: '赵奕欢',
        pic_url: 'https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=3832721317,312397218&fm=26&gp=0.jpg',
        video_url: 'https://v-cdn.zjol.com.cn/276984.mp4'
      },
      rules: {
        name: [
          { required: true, message: '请输入名称', trigger: 'change' }
        ],
        pic_url: [
          { required: true, message: '请输入图片URL', trigger: 'change' }
        ],
        video_url: [
          { required: true, message: '请输入视频URL', trigger: 'change' }
        ]
      },
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
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          this.sendMessage()
        } else {
          console.log('error submit!!')
          return false
        }
      })
    },
    resetForm(formName) {
      this.$refs[formName].resetFields()
    },
    sendMessage() {
      const { video_url } = this.ruleForm
      const notMP4 = video_url.split('.')[video_url.split('.').length - 1] !== 'mp4'
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
          message: this.ruleForm
        })
      })
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
.demo-ruleForm{
  width: 60%;
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
