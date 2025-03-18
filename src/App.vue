<template>
  <div id="app" class="chat-container">
    <h1>چت با ai</h1>
    <div class="chat-box" ref="chatBox">
      <div v-if="messages.length === 0" class="empty-chat">
        <p>هنوز پیامی ننوشتی! شروع کن</p>
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
      <textarea rows="2" dir="rtl" v-model="newMessage" placeholder="پیامتو بنویس ..." @keydown="keydownHandler" />
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
      messages: []
    }
  },
  methods: {
    async sendMessage() {
      if (this.newMessage) {
        const now = new Date();
        const userMessage = this.newMessage; // پیام کاربر رو ذخیره کن
        this.messages.push({ text: userMessage, isUser: true, time: this.getCurrentTime(), date: now });
        this.messages.push({ text: 'در حال پردازش...', isUser: false, time: this.getCurrentTime(), date: now });
        this.newMessage = ''; // حالا خالی کن
        this.scrollToBottom();

        try {
          const response = await fetch('https://jsonplaceholder.typicode.com/posts', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ message: userMessage }) // پیام کاربر رو بفرست
          });

          if (!response.ok) {
            throw new Error('خطا توی ارتباط با سرور');
          }

          const data = await response.json();
          this.messages.pop(); // "در حال پردازش" رو حذف کن
          this.messages.push({ text: data.title, isUser: false, time: this.getCurrentTime(), date: now }); // از title استفاده کن
          this.scrollToBottom();
        } catch (error) {
          this.messages.pop(); // "در حال پردازش" رو حذف کن
          this.messages.push({ text: 'خطا: نمی‌تونم به سرور وصل شم!', isUser: false, time: this.getCurrentTime(), date: now });
          this.scrollToBottom();
        }
      }
    },
    keydownHandler(event) {
      if (event.key === 'Enter' && !event.shiftKey) {
        event.preventDefault();
        this.sendMessage();
      }
    },
    scrollToBottom() {
      const chatBox = this.$refs.chatBox;
      chatBox.scrollTop = chatBox.scrollHeight;
    },
    getCurrentTime() {
      const now = new Date();
      return now.getHours().toString().padStart(2, '0') + ':' + now.getMinutes().toString().padStart(2, '0');
    },
    isFirstMessageOfDay(index) {
      if (index === 0) return true;
      const currentDate = this.messages[index].date.toDateString();
      const previousDate = this.messages[index - 1].date.toDateString();
      return currentDate !== previousDate;
    },
    formatDate(date) {
      const options = { weekday: 'long', day: 'numeric', month: 'long' };
      return date.toLocaleDateString('fa-IR', options);
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Vazirmatn:wght@400;700&display=swap');

* {
  box-sizing: border-box;
}

.chat-container {
  max-width: 800px;
  width: 90%;
  margin: 20px auto;
  font-family: 'Vazirmatn', sans-serif;
  background: #1e1e1e;
  border-radius: 15px;
  padding: 15px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
  color: #ffffff;
  min-height: calc(100vh - 40px);
  display: flex;
  flex-direction: column;
}

.chat-container h1 {
  font-size: 24px;
  text-align: center;
  margin-bottom: 15px;
  color: #00d1b2;
}

.chat-box {
  min-height: 500px;
  max-height: 500px;
  background: #2d2d2d;
  border-radius: 10px;
  padding: 10px;
  overflow-y: auto;
  border: 1px solid #3a3a3a;
}

.message-wrapper {
  margin: 8px 0;
}

.message {
  padding: 10px 15px;
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
  gap: 10px;
  margin-top: 15px;
  align-items: center;
}

textarea {
  flex: 1;
  padding: 10px;
  border: none;
  border-radius: 8px;
  background: #3a3a3a;
  color: #fff;
  font-family: 'Vazirmatn', sans-serif;
  min-height: 50px;
  resize: none;
}

textarea::placeholder {
  color: #aaa;
}

button {
  padding: 10px 20px;
  background: #00d1b2;
  color: #fff;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-family: 'Vazirmatn', sans-serif;
  transition: background 0.2s;
  height: 60px;
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

/* موبایل (زیر 600px) */
@media (max-width: 600px) {
  .chat-container {
    margin: 10px auto;
    padding: 10px;
    border-radius: 10px;
  }

  .chat-container h1 {
    font-size: 20px;
    margin-bottom: 10px;
  }

  .chat-box {
    padding: 8px;
  }

  .message {
    max-width: 85%;
    padding: 8px 12px;
    font-size: 14px;
  }

  .timestamp {
    font-size: 10px;
  }

  .date-header {
    font-size: 12px;
    padding: 4px 10px;
  }

  .empty-chat {
    font-size: 14px;
    padding: 15px;
  }

  .input-area {
    flex-direction: column;
    gap: 8px;
  }

  textarea {
    width: 100%;
    min-height: 35px;
    padding: 8px;
  }

  button {
    width: 100%;
    padding: 10px;
  }
}

/* تبلت (600px تا 900px) */
@media (min-width: 601px) and (max-width: 900px) {
  .chat-container {
    width: 85%;
    padding: 15px;
  }

  .chat-container h1 {
    font-size: 22px;
  }

  .message {
    max-width: 75%;
  }

  .input-area {
    gap: 12px;
  }

  textarea {
    min-height: 55px;
  }

  button {
    padding: 10px 25px;
  }
}
</style>