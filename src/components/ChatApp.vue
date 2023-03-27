<template>
  <div>
    <div id="login-container" v-if="!joined">
      <input type="text" v-model="currentUser" />
      <div class="mt-1">
        <button type="button" class="btn btn-primary" @click="join">
          Join
        </button>
      </div>
    </div>
    <div id="chatroom" v-if="joined">
      <div class="list-container">
        <div v-for="message in messages" :key="message.id">
          <b>{{ message.user }}</b> : {{ message.text }}
        </div>
      </div>
      <div class="text-input-container">
        <textarea
          key=""
          v-model="text"
          class="text-message"
          @keyup.enter="sendMessage"
        ></textarea>
      </div>
    </div>
  </div>
</template>
<script>
import { io } from "socket.io-client";
export default {
  data() {
    return {
      joined: false,
      currentUser: "",
      text: "",
      messages: [],
      socketInstance: null,
    };
  },
  mounted() {},
  methods: {
    join() {
      if (this.currentUser.trim().length > 0) this.joined = true;
      this.socketInstance = io("http://localhost:3000");
      this.socketInstance.on("message:recived", (data) => {
        // console.log(data);
        this.messages = [...this.messages, data];
      });
    },
    sendMessage() {
      console.log(this.text);
      this.addMessage();
      this.text = "";
    },
    addMessage() {
      const message = {
        id: new Date().toISOString(),
        text: this.text,
        user: this.currentUser,
      };
      this.messages = [...this.messages, message];
      this.socketInstance.emit("message", {
        appName: "ChatApp",
        message: JSON.stringify(message),
      });
    },
  },
};
</script>
<style></style>
