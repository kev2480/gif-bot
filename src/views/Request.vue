<template>
  <div class="wit-client">
    <div class="request">
      <div class="request-container" :style="loading ? loadingStyle : gifStyle">
        <div class="request-details" v-if="summary && gif">{{ summary }}</div>
      </div>
      <input v-model="query" :disabled="loading" /><button v-on:click="doRequest" :disabled="loading">GIF me!</button>
    </div>
    <div class="response">
      <pre>
        {{ witResponse | pretty }}
      </pre>
    </div>
  </div>

</template>

<script>
// @ is an alias to /src
import axios from 'axios'
import loadingGif from '../assets/loading.gif'
const witConfig = {
  headers: {
    Authorization: `Bearer ${process.env.VUE_APP_WIT_CLIENT_TOKEN}`,
  }
}
export default {
  name: 'request',
  data: () => {
    return {
      loading: false,
      query: 'What is the weather in Bournemouth today?',
      witClient: null,
      witResponse: null,
      summary: '',
      gif: null,
      response: null,
      loadingStyle: { 'background-image': `url(${loadingGif})`, 'max-width': `400px`, 'height': `300px` },
      gifStyle: null
    }
  },
  methods: {
    async doRequest () {
      try {
        this.loading = true
        this.response = null
        this.summary = ''
        // Get wit response
        const { data } = await axios.get(`${process.env.VUE_APP_WIT_API_ROOT}/message?v=20190311&q=${this.query}`, witConfig)
        this.witResponse = data
        if (this.witResponse.entities.intent[0].confidence > 0.75) {
          switch (this.witResponse.entities.intent[0].value) {
            case 'weather': await this.doWeather()
              break
            default:
              this.doUnknown()
          }
        } else {
          this.doUnknown()
        }
      } catch (err) {
        console.error(err)
        this.doUnknown()
      }

      this.gifStyle = { 'background-image': `url(${this.gif.url})`, 'max-width': `${this.gif.width}px`, 'height': `${this.gif.height}px` }
      this.loading = false
    },
    async doWeather () {
      // Just do weather for now..
      this.response = await axios.put(`${process.env.VUE_APP_API_ROOT}/weather`, this.witResponse)
      this.gif = this.response.data.giphy
      this.summary = `${this.response.data.location.name} - ${this.response.data.weather.summary}`
    },
    doUnknown  () {
      console.log('Something broke...', this.witResponse)
      this.gif = {
        url: 'https://i.giphy.com/media/cwTtbmUwzPqx2/giphy.webp',
        width: 380,
        height: 286
      }
      this.summary = 'Hmm, not sure'
    }
  },
  mounted () {
    this.doRequest()
  }
}
</script>

<style lang="scss">
.wit-client {
  height: 100%;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  .request {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 100%;
    margin: 8px;
    input {
      margin-top: 20px;
      width: 50%;
      @media (max-width: 500px) {
        width: 80%;
      }
    }
    .request-container {
      position: relative;
      background-size: cover;
      width: 100%;
      max-height: 600px;
      background-position: center;
      border-radius: 5px;
      transition: all 0.2s ease-in;
      .request-details {
        position: absolute;
        top: 0;
        right: 0;
        margin: 15px 20px;
        padding: 5px 10px;
        background-color: rgba(255, 255, 255, 0.8);
      }
    }
  }

  .response {
    display: none;
    pre {
      text-align: left;
      color: white;
    }
  }
}

</style>
