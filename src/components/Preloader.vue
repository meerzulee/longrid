<template>
  <div>
    <transition name="fade" mode="in-out">
      <div
        v-if="checkScreen"
        class="absolute h-screen inset-0 bg-primary-dark w-screen flex flex-col items-center justify-center preloader-z"
      >
        <div
          v-if="isMobile"
          class="flex my-4 text-center text-white items-center px-4 flex-col"
        >
          <h1 class="text-2xl mb-1">
            Мобильная версия сайта находиться в стадии разработки
          </h1>
          <p>Данный проект доступен с компьютеров</p>
        </div>
        <div
          v-else-if="isPortrait"
          class="flex my-4 text-white items-center flex-col"
        >
          <h1 class="text-2xl mb-1 text-center px-4">
            Пожалуйста переверните устройство чтоб открыть сайт
          </h1>
        </div>

        <IntersectingCirclesSpinner :size="100" color="#CD6EF0" />
      </div>
    </transition>

    <div class="hidden" v-if="!isLoaded">
      <!-- stage 1 -->
      <div v-if="!stage1Loaded">
        <!-- stage 1 -->
        <video
          v-for="(vi1, index) in 4"
          @canplaythrough="loadStage1('video ' + vi1, $event)"
          :key="'vi1' + index"
          alt=""
          :ref="'vi1' + index"
          muted
          preload="auto"
          playsinline
        >
          <source
            :src="`https://walkerstory.com/assets/media/videos/mp4/${vi1}.mp4`"
            type="video/mp4"
          />
        </video>

        <video
          @canplaythrough="loadStage1('intro', $event)"
          alt=""
          muted
          preload="auto"
          playsinline
        >
          <source
            src="https://walkerstory.com/assets/media/videos/mp4/intro.mp4"
            type="video/mp4"
          />
        </video>

        <video
          @canplaythrough="loadStage1('song', $event)"
          alt=""
          muted
          preload="metadata"
          playsinline
        >
          <source
            src="https://walkerstory.com/assets/media/videos/mp4/song.mp4"
            type="video/mp4"
          />
        </video>
      </div>
      <!-- stage 2 -->
      <div class="hidden" v-if="false">
        <!-- <div class="hidden" v-if="stage1Loaded"> -->
        <video
          v-for="(v2, index) in 5"
          @canplaythrough="loadStage2('video ' + (v2 + 4))"
          :key="'V2' + index"
          alt=""
          muted
          preload="metadata"
          playsinline
        >
          <!-- <source
            :src="require('@/assets/videos/mp4/' + (v2 + 4) + '.mp4')"
            type="video/mp4"
          />  -->
          <source
            :src="`https://walkerstory.com/wp-content/uploads/2021/01/${
              v2 + 4
            }.mp4`"
            type="video/mp4"
          />
        </video>
        <video
          v-for="(t, index) in 13"
          @canplaythrough="loadStage2('thumbs ' + t)"
          :key="'t' + index"
          alt=""
          muted
          preload="metadata"
        >
          <source
            :src="`https://walkerstory.com/wp-content/uploads/2021/01/th${t}.mp4`"
            type="video/mp4"
          />
        </video>
      </div>
      <!-- stage 3 -->
      <div class="hidden" v-if="stage2Loaded && !stage3Loaded">
        <video
          v-for="(v3, index) in 4"
          @canplaythrough="loadStage3('video ' + (v3 + 9))"
          :key="'V3' + index"
          alt=""
          muted
          preload="auto"
        >
          <source
            :src="`https://walkerstory.com/wp-content/uploads/2021/01/${
              v3 + 9
            }.mp4`"
            type="video/mp4"
          />
        </video>
        <video
          @canplaythrough="loadStage3('ending')"
          alt=""
          muted
          preload="auto"
        >
          <source
            src="https://walkerstory.com/wp-content/uploads/2021/01/end.mp4"
            type="video/mp4"
          />
        </video>
        <video
          v-for="(o, index) in ['sveta', 'talgat', 'maya', 'diana']"
          @canplaythrough="loadStage3('outro ' + o)"
          :key="'O' + index"
          alt=""
          muted
          preload="auto"
        >
          <source
            :src="`https://walkerstory.com/wp-content/uploads/2021/01/${o}.mp4`"
            type="video/mp4"
          />
        </video>
      </div>
    </div>
  </div>
</template>

<script>
import 'epic-spinners/dist/lib/epic-spinners.min.css'
// import axios from 'axios'
import { IntersectingCirclesSpinner } from 'epic-spinners/dist/lib/epic-spinners.min.js'

export default {
  components: {
    IntersectingCirclesSpinner,
  },
  data() {
    return {
      isLoaded: false,

      stage1Loaded: false,
      stage2Loaded: false,
      stage3Loaded: false,

      stage1Count: 0,
      stage2Count: 0,
      stage3Count: 0,
    }
  },
  computed: {
    isMobile() {
      return (
        this.$screen.height < this.$screen.config.mobile &&
        this.$screen.width < this.$screen.config.mobile
      )
    },
    isPortrait() {
      return (
        this.$screen.height > this.$screen.config.mobile &&
        this.$screen.portrait &&
        this.$screen.width < this.$screen.config.mobile
      )
    },
    checkScreen() {
      // console.log(this.isMobile)
      // console.log(this.isPortrait)
      return !(this.$store.state.loaded && !(this.isMobile || this.isPortrait))
    },
  },
  mounted() {
    console.log('fuck')
    if (this.$browserDetect.isSafari) {
      this.$store.commit('setLoadedTrue')
    }

    this.isLoaded = false
    this.stage1Count = 0
    this.stage2Count = 0
    this.stage3Count = 0

    this.stage1Loaded = false
    this.stage2Loaded = false
    this.stage3Loaded = false
  },

  methods: {
    loadStage1(e, event) {
      console.log(e)
      // console.log(event.target.readyState)
      if (event.target.readyState > 3) {
        this.stage1Count++
        if (this.stage1Count >= 5) {
          this.stage1Loaded = true
          console.log('Stage 1 completed')
          this.check()
        }
      }
    },
    loadStage2() {
      // console.log(e)
      this.stage2Count++
      if (this.stage2Count === 18) {
        this.stage2Loaded = true
        console.log('Stage 2 completed')
      }
    },
    loadStage3() {
      // console.log(e)
      this.stage3Count++
      if (this.stage3Count === 9) {
        this.stage3Loaded = true
        console.log('Stage 3 completed')
      }
    },

    check() {
      if (this.stage1Loaded) {
        this.$store.commit('setLoadedTrue')
      }
      if (this.stage1Loaded && this.stage2Loaded && this.stage3Loaded) {
        this.isLoaded = true
      }
    },
  },
}
</script>

<style >
.preloader-z {
  z-index: 9999;
}
</style>