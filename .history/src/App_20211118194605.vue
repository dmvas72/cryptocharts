<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <Chart msg="Hello"/>
    <button v-on:click="sendMessage('hello')">Send Message</button>
    <div>{{this.price.ltc }}</div>
  </div>
</template>

<script>
import Chart from './components/Chart.vue'

export default {
  name: 'App',
  data() {
    return {
      connection: null,
      price: {
        eth: null,
        ltc: null,
      }
    }
  },
  components: {
    Chart
  },
  methods: {
    sendMessage: function(message) {
      console.log("Hello")
      console.log(this.connection);
      this.connection.send(message);
    }
  },
  created: function() {
    console.log("Starting connection to WebSocket Server")
    /* wss://stream.binance.com:9443/ws/ethusdt@trade */
    this.connection = new WebSocket("wss://stream.binance.com:9443/stream?streams=ltcusdt@aggTrade/ethusdt@aggTrade")

    this.connection.onmessage = function(event) {
      console.log(event);
    }

    this.connection.onopen = function(event) {
      console.log(event)
      console.log("Successfully connected to the echo websocket server...")
    }

    this.connection.onmessage = (event) => {
      let stockObject = JSON.parse(event.data);
      this.price = stockObject.data.p;

      if(stockObject.stream == "ltcusdt@aggTrade") {
        this.price.ltc = stockObject.data.p
      }
      console.log(stockObject);
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
