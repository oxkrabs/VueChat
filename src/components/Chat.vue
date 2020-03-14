<template>
  <div>
    Chat
    <div class="room-header">{{room}}</div>
    <div>
      <form @submit.prevent="createRoom">
        <span style="margin-right:20px">{{user}}</span>
        <input type="text" placeholder="user to send" v-model="userToSend" />
        <button type="submit">Create Room</button>
      </form>
      <form @submit.prevent="sendDirectMessage">
        <span style="margin-right:20px">{{user}}</span>

        <input type="text" placeholder="message" v-model="message" />
        <button type="submit">Send</button>
      </form>
    </div>
    <div class="layout-container">
      <div class="users-container">
        <div>
          <div v-for="(value, key) in users" v-bind:key="key">{{key}}</div>
        </div>
      </div>
      <div class="chat-container">
        <div class="chat-box">
          <div
            class="chat-message-b"
            v-for="(item, index) in messages"
            v-bind:key="index"
            v-bind:class="{ mine: item.user === user }"
          >
            <span class="chat-box__user">{{ item.user }}</span>
            <p class="chat-box__message">{{ item.message }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import io from "socket.io-client";

export default {
  name: "Chat",
  data: function() {
    return {
      message: "",
      messages: [],
      room: "",
      userToSend: "",
      user: `user${Math.round(Math.random() * 20)}`,
      users: {},
      socket: io("localhost:3001")
    };
  },
  methods: {
    createRoom: function(e) {
      e.preventDefault();
      const vm = this;
      vm.socket.emit("create_room", {
        user1: vm.user,
        user2: vm.userToSend
      });
    },
    sendDirectMessage: function(e) {
      e.preventDefault();
      const vm = this;
      if (vm.room === "") {
        console.error("No room found.");
      } else {
        vm.socket.emit("send_message_to", {
          room: vm.room,
          message: vm.message
        });
      }
    }
  },
  mounted() {
    const vm = this;
    vm.socket.emit("connected", vm.user);

    this.socket.on("CONNECTED", function(data) {
      console.log("connected: ", data);
      vm.users = data;
    });

    this.socket.on("MESSAGE", function(data) {
      vm.messages = [...vm.messages, data];
    });

    this.socket.on("ROOM_INIT", function(data) {
      vm.room = data;
      console.log("Joined ", vm.room);
    });

    this.socket.on("ROOM_INIT_DATA", function(data) {
      console.log("data: ", data);
    });

    this.socket.on("NEW_ROOM_MESSAGE", function(data) {
      vm.messages = [...vm.messages, data.messageObject];
    });
  }
};
</script>

<style scoped>
.mine {
  background: red !important;
  margin-left: 10%;
}
.chat-message-b {
  background: blue;
  width: 80%;
  border-radius: 10px;
  position: relative;
  padding: 20px;
  color: white;
  margin-bottom: 5px;
}
.room-header {
  text-align: center;
  background: red;
  padding: 20px;
  color: white;
}
.layout-container {
  display: flex;
  align-items: flex-start;
  justify-content: center;
  margin-top: 20px;
}
.users-container {
  width: 300px;
  height: 600px;
  outline: 1px solid #333;
}
.chat-container {
  display: flex;
  justify-content: center;
}
.chat-box {
  width: 500px;
  height: 600px;
  outline: 1px solid #333;
}
.chat-box__user {
  color: red;
  position: absolute;
  top: 0;
  left: 0;
  background: white;
  border: 1px solid black;
  border-radius: 10px;
  padding: 5px;
}
.chat-box__message {
  margin: 0;
}
</style>
