<template>
  <div class="wrapper">
    <div class="card mt-3">
      <div class="card-body">
        <div class="card-title">
          <h3>Chat Room</h3>
        </div>
        <div class="card-body message-body">
          <div class="message-container" :class="msg.socket_id == socket.id && 'sender-msg'" v-for="(msg, index) in messages" :key="index">
            <div class="messages">
              <p class="username">{{ msg.user }}</p>
              <p>{{ msg.message }}</p>
            </div>
          </div>
        </div>
      </div>
      <div class="card-footer">
        <form v-if="!isUserJoined" @submit.prevent="pingServer">
          <div class="form-group">
            <label for="user">Enter username to join:</label>
            <div class="input-container">
              <input type="text" v-model="user" class="form-control" />
            </div>
            <button type="submit" class="btn btn-success">Join</button>
          </div>
        </form>
        <form v-else @submit.prevent="sendMessage">
          <div class="form-group msg-container">
            <input type="text" v-model="message" placeholder="Type Message" class="form-control" />
            <button type="submit" class="btn btn-success">Send</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import io from "socket.io-client";
export default {
  data() {
    return {
      user: "",
      message: "",
      messages: [],
      socket: io("localhost:4000", { transports: ["websocket"] }),
      isUserJoined: false,
    };
  },
  methods: {
    sendMessage(e) {
      e.preventDefault();
      this.socket.emit("SEND_MESSAGE", {
        user: this.user,
        message: this.message,
        socket_id: this.socket.id
      });
      this.message = "";
    },
    pingServer() {
      this.socket.on("MESSAGE", (data) => {
        this.messages = [...this.messages, data];
      });
      this.isUserJoined = true;
    }
  },
};
</script>

<style scoped>
.wrapper {
  background: #e8eaff;
  min-height: 100vh;
}

.card-title {
  margin-bottom: 30px;
}

.message-body {
  padding: 20px;
  max-width: 700px;
  margin: auto;
}

.card-title h3 {
  text-align: center;
  background: #153f5e;
  color: #fff;
  text-transform: uppercase;
  padding: 20px;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 10px;
  align-items: center;
  justify-content: center;
}

.msg-container {
  width: 100%;
  position: fixed;
  bottom: 50px;
  flex-direction: row;
}

.form-group label {
  color: #153f5e;
  font-weight: bolder;
}

.form-group .input-container {
  display: flex;
  justify-content: center;
  width: 100%;
}

.form-group input {
  height: 20px;
  max-width: 300px;
  width: 100%;
  padding: 10px;
  border-radius: 5px;
  color: #153f5e;
}

.form-group input:focus-visible {
  border: 2px solid #153f5e;
  outline: none
}

.btn {
  background: #35d87a;
  color: #fff;
  border: none;
  outline: none;
  cursor: pointer;
  padding: 10px 30px;
  border-radius: 50px;
}

.message-container {
  display: flex;
  justify-content: flex-start;
}

.messages {
  color: #153f5e;
  width: 200px;
  border-radius: 10px;
  padding: 0 10px 10px 10px;
  background: #fff;
  box-shadow: 2px 2px #d7d7d7;
  position: relative;
  margin-bottom: 20px;
}

.messages::after {
  border-width: 0px 20px 20px 0;
  border-color: transparent #fff transparent transparent;
  top: 0;
  left: -10px;    
  position: absolute;
  content: "";
  width: 0;
  height: 0;
  border-style: solid;
}

.messages .username {
  font-weight: bolder;
  padding-bottom: 10px;
}

.sender-msg {
  justify-content: flex-end;
}

.sender-msg .messages::after {
  left: auto;
  right: -10px;
  border-width: 0px 0px 20px 20px;
  border-color: #fff transparent transparent #fff;
}
</style>