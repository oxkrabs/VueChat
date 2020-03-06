<template>
  <div>
    Chat
    <div>
      <form>
        <input type="text" v-model="message" />
        <button type="submit">Send</button>
      </form>
    </div>
    <div class="chat-container">
      <div class="chat-box"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Chat",
  data: () => ({
    message: ""
  })
};
</script>

<style scoped>
.chat-container {
  margin-top: 20px;
  display: flex;
  justify-content: center;
}
.chat-box {
  width: 500px;
  height: 500px;
  outline: 1px solid black;
}
</style>