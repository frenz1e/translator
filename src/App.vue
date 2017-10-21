<template>
  <div id="app">
    <div class="form">
      <form v-on:submit.prevent="translate">
        <input type="text" v-model="word" :disabled="loading ? true : false" placeholder="write a word">
        <button :disabled="loading ? true : false">{{ loading ? '...' : 'Go' }}</button>
        <div class="audio-row" v-for="(item, index) in audioData" :key="item.id">
          <span class="play" v-on:click="play(item.fileUrl)" />
          <a href="#" v-on:click.prevent="download(item.fileUrl)">Download</a>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
const API_KEY = 'a20155b950f1e07cbcc000980310524bd93b0ae2ce9d80c89'
const getAudioData = (word, key) =>
  fetch(`http://api.wordnik.com:80/v4/word.json/${word.toLowerCase()}/audio?useCanonical=false&limit=50&api_key=${key}`)
    .then(resp => {
      return resp.json()
    })

export default {
  name: 'app',
  data: () => ({
    word: '',
    loading: false,
    audioData: []
  }),
  methods: {
    translate () {
      console.log(this.word, this.loading)
      this.getAudio()
    },
    getAudio () {
      if (!this.word) return false
      this.loading = true
      getAudioData(this.word, API_KEY).then(data => {
        this.loading = false
        this.audioData = data
      })
      .catch((err) => {
        this.loading = false
        console.log('Fail request', err)
      })
    },
    download (url) {
      const link = document.createElement('a')
      link.href = url
      link.target = '_blank'
      link.download = 'download'
      document.body.appendChild(link)
      link.click()
      document.body.removeChild(link)
    },
    play (url) {
      var audio = new Audio(url)
      audio.play()
    }
  }
}
</script>

<style lang="sass">
*
  box-sizing: border-box

#app
  font-family: 'Avenir', Helvetica, Arial, sans-serif
  color: #2c3e50
  margin-top: 60px

  .form
    width: 300px
    margin: 0 auto
    display: flex

    input
      height: 50px
      font-size: 20px
      padding: 0 10px

    button
      height: 50px
      font-size: 20px
      padding: 0 10px
  
  .audio-row
    padding: 10px 0
    border-bottom: 1px solid #eee
    display: flex
    justify-content: space-between
    align-items: center

    a
      text-decoration: none
      height: 40px
      line-height: 40px

    .play
      border: 1px solid #eee
      border-radius: 50%
      width: 40px
      height: 40px
      display: flex
      align-items: center
      justify-content: center
      line-height: 1
      cursor: pointer

      &:before
        content: ''
        display: block
        width: 0 
        height: 0 
        border-top: 10px solid transparent
        border-bottom: 10px solid transparent
        border-left: 16px solid green
        transform: translateX(2px)

</style>
