<template>
<main>
  <Nav />
  <h1>Chat</h1>
  <div class="card mt-3 text-white bg-dark">
      <div class="card-body">
          <div class="card-body">
              <div class="messages" v-for="(msg, index) in logs" :key="index">
                  <p><span class="font-weight-bold">{{ msg.user }}: </span>{{ msg.message }}</p>
              </div>
              <div class="messages" v-for="(msg, index) in messages" :key="index">
                  <p><span class="font-weight-bold">{{ msg.user }}: </span>{{ msg.message }}</p>
              </div>
          </div>
      </div>
      <div class="card-footer">
          <form @submit.prevent="sendMessage">
              <div class="gorm-group">
                  <input type="text" v-model="user" placeholder="Nickname">
              </div>
              <div class="gorm-group pb-3">
                  <input type="text" v-model="message"  placeholder="Your message here...">
              </div>
              <button type="submit" >Send</button>
          </form>
      </div>
  </div>
</main>
</template>

<script>

import Nav from './Nav.vue';
import io from 'socket.io-client';


export default {
    name: 'Chat',
    props: { },
    components: {
        Nav,
    },
    data() {
        return {
            user: '',
            message: '',
            messages: [],
            logs: [],
            socket : io('me-api.edwardnilsson.se')
            //socket : io('localhost:8333')
        }
    },
    methods: {
        sendMessage(e) {
            e.preventDefault();

            this.socket.emit('SEND_MESSAGE', {
                user: this.user,
                message: this.message
            });

            this.message = '';
        }
    },
    mounted() {
        this.socket.on('MESSAGES', (data) => {
            if (data.length > 0 || Object.keys(data).length > 0) {
              //push new message
              this.logs.push(data);
              //get from mongodb
              for (var i = 0; i < data.length; i++) {
                this.logs.push({user:data[i].user, message:data[i].message});
              }
            }
        });

    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

main {
  color: black;
}

h1 {
  color: white;
}

p {
  font-size: 1em;
}

button {
  background-color: green; /* Green */
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 20px;
}

</style>
