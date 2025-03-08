<template>
  <div id="app" class="chat-container">
    <h1>چت رزومه‌ساز</h1>
    <div class="chat-box" ref="chatBox">
      <div v-if="messages.length === 0" class="empty-chat">
        <p>هنوز پیامی ننوشتی! شروع کن...</p>
      </div>
      <div v-for="(msg, index) in messages" :key="index" class="message-wrapper">
        <div v-if="isFirstMessageOfDay(index)" class="date-header">
          {{ formatDate(msg.date) }}
        </div>
        <div :class="{ 'user-msg': msg.isUser }" class="message">
          <p>{{ msg.text }}</p>
          <span class="timestamp">{{ msg.time }}</span>
        </div>
      </div>
    </div>
    <div class="input-area">
      <input v-model="newMessage" placeholder="پیامتو بنویس..." @keyup.enter="sendMessage" />
      <button @click="sendMessage">ارسال</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      newMessage: '',
      messages: [] // خالی شروع می‌شه
    }
  },
  methods: {
    sendMessage() {
      if (this.newMessage) {
        const now = new Date()
        this.messages.push({ text: this.newMessage, isUser: true, time: this.getCurrentTime(), date: now })
        this.messages.push({ text: ' ...در حال پردازش  ', isUser: false, time: this.getCurrentTime(), date: now }) // جواب ثابت
        this.newMessage = ''
        this.scrollToBottom()
      }
    },
    scrollToBottom() {
      const chatBox = this.$refs.chatBox
      chatBox.scrollTop = chatBox.scrollHeight
    },
    getCurrentTime() {
      const now = new Date()
      return now.getHours().toString().padStart(2, '0') + ':' + now.getMinutes().toString().padStart(2, '0')
    },
    isFirstMessageOfDay(index) {
      if (index === 0) return true
      const currentDate = this.messages[index].date.toDateString()
      const previousDate = this.messages[index - 1].date.toDateString()
      return currentDate !== previousDate
    },
    formatDate(date) {
      const options = { weekday: 'long', day: 'numeric', month: 'long' }
      return date.toLocaleDateString('fa-IR', options)
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Vazirmatn:wght@400;700&display=swap');

.chat-container {
  max-width: 800px;
  margin: 30px auto;
  font-family: 'Vazirmatn', sans-serif;
  background: #1e1e1e;
  border-radius: 15px;
  padding: 20px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
  color: #ffffff;
}
.chat-container h1 {
  font-size: 24px;
  text-align: center;
  margin-bottom: 20px;
  color: #00d1b2;
}
.chat-box {
  height: 450px;
  background: #2d2d2d;
  border-radius: 10px;
  padding: 15px;
  overflow-y: auto;
  border: 1px solid #3a3a3a;
}
.message-wrapper {
  margin: 10px 0;
}
.message {
  padding: 12px 18px;
  border-radius: 10px;
  display: block;
  width: fit-content;
  max-width: 70%;
  word-wrap: break-word;
  animation: fadeIn 0.3s ease-in;
}
.user-msg {
  background: #00d1b2;
  color: #fff;
  text-align: right;
  margin-left: auto;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
}
.message:not(.user-msg) {
  background: #444;
  color: #fff;
  text-align: left;
  margin-right: auto;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
}
.message p {
  margin: 0;
}
.timestamp {
  display: block;
  font-size: 12px;
  color: #bbb;
  margin-top: 5px;
  opacity: 0.8;
  text-align: right;
  background: rgba(0, 0, 0, 0.2);
  padding: 2px 8px;
  border-radius: 5px;
  width: fit-content;
  margin-left: auto;
}
.message:not(.user-msg) .timestamp {
  margin-right: auto;
  margin-left: 0;
}
.date-header {
  text-align: center;
  font-size: 14px;
  color: #00d1b2;
  background: #2a2a2a;
  padding: 5px 15px;
  border-radius: 20px;
  margin: 10px auto;
  width: fit-content;
  box-shadow: 0 2px 8px rgba(0, 209, 178, 0.3);
  border: 1px solid #00d1b2;
}
.empty-chat {
  text-align: center;
  color: #aaa;
  padding: 20px;
  font-size: 16px;
}
.input-area {
  display: flex;
  gap: 15px;
  margin-top: 20px;
}
input {
  flex: 1;
  padding: 12px;
  border: none;
  border-radius: 8px;
  background: #3a3a3a;
  color: #fff;
  font-family: 'Vazirmatn', sans-serif;
}
input::placeholder {
  color: #aaa;
}
button {
  padding: 12px 25px;
  background: #00d1b2;
  color: #fff;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-family: 'Vazirmatn', sans-serif;
  transition: background 0.2s;
}
button:hover {
  background: #00b89c;
}
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>