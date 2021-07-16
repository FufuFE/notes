<template>
  <div id="app">
      <div>
        Hellow World
        Gyro: {{gyro}}
        
      </div>
      <div>
        <button @click="publish">Send command</button>
      </div>
  </div>
</template>

<script>
import awsconfig from './aws-exports'
Amplify.configure(awsconfig)

import Amplify, { PubSub } from 'aws-amplify';
import { AWSIoTProvider } from '@aws-amplify/pubsub/lib/Providers';

Amplify.addPluggable(new AWSIoTProvider({
  aws_pubsub_region: 'ap-northeast-1',
  aws_pubsub_endpoint: 'wss:///mqtt',
}));

export default {
  name: 'App',
  data: () => {
    return {
      gyro: "value"
    }
  },
  mounted: async function () {
    PubSub.subscribe('data/sensor').subscribe({
      next: data => {
        this.gyro = data.value.data
        console.log('Message received', data)
      },
      error: error => console.error(error),
      close: () => console.log('Done'),
    });
  },
  methods: {
    publish() {
      PubSub.publish('cmd/device', "cmd");
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
