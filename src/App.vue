<template>
  <div>

    <head>
      <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.2/css/all.min.css"
        integrity="sha512-HK5fgLBL+xu6dm/Ii3z4xhlSUyZgTT9tuc/hSrtw6uzJOvgRr2a9jyxxT1ely+B+xFAmJKVSTbpM/CuL7qxO8w=="
        crossorigin="anonymous" />
      <link rel="preconnect" href="https://fonts.gstatic.com">
      <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600&display=swap" rel="stylesheet">
    </head>
    <div class="player">
      <!-- Dashboard -->
      <div class="dashboard">
        <!-- Header -->
        <header>
          <h4>Now playing:</h4>
          <h2> {{songPlay}}</h2>
        </header>

        <!-- CD -->
        <div class="cd" ref="cd" :style="{ width: cdWidth + 'px', opacity: cdOpacity }">
          <div class="cd-thumb" :style="{ 'background-image': 'url(' + imgSongPlay + ')' }">
          </div>
        </div>

        <!-- Control -->
        <div class="control">
          <div class="btn btn-repeat" @click="loopSong">
            <i class="fas fa-redo" :class="{ 'active': loopClicked === true }"></i>
          </div>
          <div class="btn btn-prev" @click="preSong">
            <i class="fas fa-step-backward"></i>
          </div>
          <div class="btn btn-toggle-play" v-if="played" @click="pauseSong">
            <i class="fas fa-pause icon-pause" ></i>
          </div>
          <div class="btn btn-toggle-play" v-else @click="playSong">
            <i class="fas fa-play icon-play" ></i>
          </div>
          <div class="btn btn-next" @click="nextSong">
            <i class="fas fa-step-forward"></i>
          </div>
          <div class="btn btn-random" @click="randomSong">
            <i class="fas fa-random" :class="{ 'active': randomClicked === true }"></i>
          </div>
        </div>

        <vue-slider id="progress" class="progress" type="range" v-model="progress" step="1" :min="0" :max="100"
          @change="onChange"/>
        <audio ref="audio" @timeupdate="updateProgress" @ended="onAudioEnded"></audio>
        <div class="volume">
          <i class="fas fa-volume-up volume-btn"></i>
          <vue-slider type="range" ref="volume" class="volume-range"  :min="0" :max="100" v-model="vol"  @change="onChangeVolume" />
        </div>
      </div>
      <!-- Playlist -->
      <div class="playlist" v-for="(songs, index) in songs" :key="index"  @click="pickSong(index)">
        <ListSong :song="songs" :index="index" :class="{'active': index === currentSong}"/>
      </div>
    </div>
  </div>
</template>

<script>
import VueSlider from 'vue-slider-component';
import ListSong from '@/components/ListSong'
export default {
  data() {
    return {
      songs: [
        {
          name: 'Arcade',
          singer: 'Jake',
          path: require('@/assets/music/arcade.mp3'),
          img: require('@/assets/img/ardace.jpg')
        },
        {
          name: 'Dancin',
          singer: 'Jake',
          path: require('./assets/music/dacin.mp3'),
          img: require('./assets/img/dancin.jpg')
        },
        {
          name: 'Memories',
          singer: 'Marron 5',
          path: require('./assets/music/memories.mp3'),
          img: require('./assets/img/memories.jpg')
        },
        {
          name: 'Night Chance',
          singer: 'One Direction',
          path: require('./assets/music/nightchance.mp3'),
          img: require('./assets/img/nightchance.jpg')
        },
        {
          name: 'Toxic',
          singer: 'One Direction',
          path: require('./assets/music/Toxic.mp3'),
          img: require('./assets/img/toxic.jpg')
        },
        {
          name: 'Waiting for love',
          singer: 'Avicii',
          path: require('./assets/music/waitingforlove.mp3'),
          img: require('./assets/img/waitingforlove.jpg')
        },
        {
          name: 'Wake me up',
          singer: 'Avicii',
          path: require('./assets/music/wakemeup.mp3'),
          img: require('./assets/img/wakemeup.jpg')
        },
        {
          name: 'See you again',
          singer: 'Avicii',
          path: require('./assets/music/seeyouagain.mp3'),
          img: require('./assets/img/seeyouagain.jpg')
        },
        {
          name: 'Golden Hour',
          singer: 'Jake',
          path: require('./assets/music/goldenhour.mp3'),
          img: require('./assets/img/goldenhour.jpg')
        },
        {
          name: 'Another Love',
          singer: 'BA',
          path: require('./assets/music/anotherlove.mp3'),
          img: require('./assets/img/anotherlove.jpg')
        },
        {
          name: 'Death Bed',
          singer: 'Abc',
          path: require('./assets/music/deathbed.mp3'),
          img: require('./assets/img/deathbed.jpg')
        },
        {
          name: 'Glim of Us',
          singer: 'Juji',
          path: require('./assets/music/glimpseofus.mp3'),
          img: require('./assets/img/glimofus.jpg')
        },
        {
          name: 'Comethru',
          singer: 'JVK',
          path: require('./assets/music/comethru.mp3'),
          img: require('./assets/img/comethru.jpg')
        },
        {
          name: 'This is what falling in love',
          singer: 'Jake',
          path: require('./assets/music/thisiswhatfallinginlovefeelslike.mp3'),
          img: require('./assets/img/thisis.jpg')
        },
        {
          name: '2002',
          singer: 'Annie',
          path: require('./assets/music/2002.mp3'),
          img: require('./assets/img/2002.jpg')
        },
      ],
      songPlay: '',
      imgSongPlay:'',
      played: false,
      progress: 0,
      vol: 100,
      currentSong: 0,
      randomClicked: false,
      loopClicked: false,
      cdWidth: 200,
      cdOpacity: 1,
    }
  },
  components: {
    ListSong,
    VueSlider,

  },

  mounted() {
    this.$refs.audio.src = this.songs[this.currentSong].path
    this.songPlay = this.songs[this.currentSong].name
    this.imgSongPlay = this.songs[this.currentSong].img
    window.addEventListener('scroll', this.handleScroll);
  },

  methods: {
    setSong() {
      this.$refs.audio.src = this.songs[this.currentSong].path
      this.songPlay = this.songs[this.currentSong].name
      this.imgSongPlay = this.songs[this.currentSong].img
    },
    playSong() {
      this.$refs.audio.play()
      this.played = true

    },
    pauseSong() {
      this.$refs.audio.pause()
      this.played = false
    },
    updateProgress() {
      const audio = this.$refs.audio;
      const duration = audio.duration;
      const currentTime = audio.currentTime;
      this.progress = (currentTime / duration) * 100;
    },
    nextSong() {
      if (this.randomClicked) {
        let randomId = Math.floor(Math.random() * 1000) % this.songs.length
        if (this.currentSong === randomId) this.nextSong()
        else {
          this.currentSong = randomId
          this.setSong()
          this.playSong()
        }
      }
      else {
        if (this.currentSong === this.songs.length - 1) this.currentSong = 0
        else this.currentSong++
        this.setSong()
        this.playSong()
      }
    },
    preSong() {
      if (this.currentSong === 0) this.currentSong = this.songs.length - 1
      else this.currentSong--
      this.setSong()
      this.playSong()
    },
    onChange() {
      const time = this.$refs.audio.duration * this.progress / 100
      this.$refs.audio.currentTime = time
    },
    randomSong() {
      if (this.randomClicked) this.randomClicked = false
      else this.randomClicked = true
    },
    loopSong() {
      if (this.loopClicked) this.loopClicked = false
      else this.loopClicked = true
    },
    onAudioEnded() {
      if (this.loopClicked) {
        this.$refs.audio.currentTime = 0
        this.playSong()
      }
      else {
        this.nextSong()
      }
    },
    handleScroll() {
      const scrollTop = document.documentElement.scrollTop
      const cdWidthDefault = 200
      const newCdWidth = cdWidthDefault - scrollTop
      this.cdWidth = newCdWidth > 0 ? newCdWidth : 0
      this.cdOpacity = newCdWidth / this.cdWidth
    },
    pickSong(index) {
      this.currentSong = index 
      this.setSong()
      this.playSong()
    },
    onChangeVolume() {
      const volumeChange = this.vol / 100
      this.vol = volumeChange * 100
      this.$refs.audio.volume = volumeChange
    }
  }
}

</script>

<style>
:root {
  --primary-color: #ec1f55;
  --text-color: #333;
}

.active {
  color: var(--primary-color)
}

* {
  padding: 0;
  margin: 0;
  box-sizing: inherit;
}

body {
  background-color: #f5f5f5;
}

html {
  box-sizing: border-box;
  font-family: "Poppins", sans-serif;
}

.player {
  position: relative;
  max-width: 480px;
  margin: 450px auto 0;
}

.player .icon-pause {}

.player.playing .icon-pause {
  display: inline-block;
}

.player.playing .icon-play {
  display: none;
}

.dashboard {
  padding: 16px 16px 14px;
  background-color: #fff;
  position: fixed;
  top: 0;
  width: 100%;
  max-width: 480px;
  border-bottom: 1px solid #ebebeb;
}

/* HEADER */
header {
  text-align: center;
  margin-bottom: 10px;
}

header h4 {
  color: var(--primary-color);
  font-size: 12px;
}

header h2 {
  color: var(--text-color);
  font-size: 20px;
}

/* CD */
.cd {
  display: flex;
  margin: auto;
  width: 200px;
}

.cd-thumb {
  width: 100%;
  padding-top: 100%;
  border-radius: 50%;
  background-color: #333;
  background-size: cover;
  margin: auto;
}

/* CONTROL */
.control {
  display: flex;
  align-items: center;
  justify-content: space-around;
  padding: 18px 0 8px 0;
}

.control .btn {
  color: #666;
  padding: 18px;
  font-size: 18px;
}

.control .btn.active {
  color: var(--primary-color);
}

.control .btn-toggle-play {
  width: 56px;
  height: 56px;
  border-radius: 50%;
  font-size: 24px;
  color: #fff;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: var(--primary-color);
}

.progress {
  width: 100% !important;
  -webkit-appearance: none;
  height: 6px !important;
  background: #d3d3d3;
  outline: none;
  opacity: 0.7;
  -webkit-transition: 0.2s;
  transition: opacity 0.2s;
  padding: 0 !important;
}

.progress .vue-slider-dot {
  background-color: var(--primary-color);
  height: 6px !important;
  padding: 0 !important;
}

.progress .vue-slider-dot:hover {
  background-color: var(--primary-color);
  height: 12px !important;
  padding: 4px 0;
}

.progress .vue-slider-dot:hover .vue-slider-dot-tooltip-top {
  display: none;
}

.progress .vue-slider-dot-tooltip-text {
  display: none;
}

.progress::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 12px;
  height: 6px;
  background-color: var(--primary-color);
  cursor: pointer;
}

/* PLAYLIST */

.playlist {
  padding: 12px;
}

.song {
  display: flex;
  align-items: center;
  margin-bottom: 12px;
  background-color: #fff;
  padding: 8px 16px;
  border-radius: 5px;
  box-shadow: 0 2px 3px rgba(0, 0, 0, 0.1);
}

.song.active {
  background-color: var(--primary-color);
}

.song:active {
  opacity: 0.8;
}

.song.active .option,
.song.active .author,
.song.active .title {
  color: #fff;
}

.song .thumb {
  width: 44px;
  height: 44px;
  border-radius: 50%;
  background-size: cover;
  margin: 0 8px;
}

.song .body {
  flex: 1;
  padding: 0 16px;
}

.song .title {
  font-size: 18px;
  color: var(--text-color);
}

.song .author {
  font-size: 12px;
  color: #999;
}

.song .option {
  padding: 16px 8px;
  color: #999;
  font-size: 18px;
}

.cd-thumb.rotating {
  animation: loading 10s linear infinite;
}

.volume {
  color: var(--primary-color)
}

.volume:hover .volume-range {
  display: inline-block;
}

.volume-range {
  display: none;
}

/* Vue SLider */
.volume .vue-slider {
  width: 50% !important;
  background-color: #d3d3d3;
  padding: 4px 8px !important;
  border-radius: 10px;
  display: none
}
.volume .vue-slider-dot {
  background-color: var(--primary-color);
  border-radius: 10px;
  
}

@media (max-width: 768px) {
  .volume-range {
    display: inline-block;
  }
}

@keyframes loading {
  0% {
    transform: rotate(0);
  }

  100% {
    transform: rotate(360deg);
  }
}
</style>
