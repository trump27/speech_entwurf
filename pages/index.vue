<template lang="pug">
  v-layout(column justify-center align-center)
    p.font-weight-black(text-left) 会議メモを作成するには、マイクを準備して<code>音声認識スタート</code>をクリック
    v-flex(xs12 sm8 md6)
      v-btn.mr-3(@click="startSpeech" outlined tile :color="pcolor")
        v-icon(left) mdi-microphone
        span {{ pcaption }}
      v-btn.mr-3(@click="save" outlined tile color="success" v-if="speech != ''")
        v-icon(left) mdi-download
        span 保存
      v-btn.mr-3(@click="clear" outlined tile color="success" v-if="speech != '' && !started")
        v-icon(left) mdi-close
        span クリア

      v-textarea.mt-3(outlined name="guess" readonly label="推定" rows="1" auto-grow
        v-model="proc" wrap="soft" :loading="started")
      //- v-text-field(v-model="proc")

      h4.mt-3.mb-2 認識結果
      v-textarea.speech(v-model="speech"
        id="totext" counter solo filled rows="15"
        outlined densu :loading="started")

    div.text-sm-left chromeのみ利用可能
    div.text-sm-left 外部に音声・認識結果は送信されません（オフラインで認識）

</template>

<script>
export default {
  data () {
    return {
      recognition: null,
      started: false,
      speech: '',
      proc: ''
    }
  },
  computed: {
    pcaption () {
      return this.started ? '音声認識ストップ' : '音声認識スタート'
    },
    pcolor () {
      return this.started ? 'warning' : 'indigo'
    }
  },
  mounted () {
    this.init()
  },
  beforeDestroy () {
    this.recognition.stop()
    this.recognition = null
  },
  methods: {
    init () {
      const SpeechRecognition =
        window.webkitSpeechRecognition || window.SpeechRecognition
      this.recognition = new SpeechRecognition()
      this.recognition.lang = 'ja-JP'
      this.recognition.interimResults = true
      this.recognition.continuous = true

      const self = this

      this.recognition.onresult = function (e) {
        let interimTranscript = ''
        let fixed = false

        for (let i = e.resultIndex; i < e.results.length; i++) {
          const transcript = e.results[i][0].transcript
          if (e.results[i].isFinal) {
            self.speech += transcript + '\n'
            self.proc = ''
            // document.getElementById('totext').scrollTop = 99999
            fixed = true
            console.log('fixed:', transcript)
          } else {
            interimTranscript = transcript
          }
        }

        self.proc = interimTranscript
        interimTranscript += '\n'
        if (fixed) {
          // self.speech = this.finalTranscript + interimTranscript
          document.getElementById('totext').scrollTop = 99999
        }
      }

      this.recognition.onend = () => {
        if (self.recognition && self.started) { self.recognition.start() }
      }
      this.recognition.onerror = (e) => {
        console.log('音声を認識でエラー発生：', e.error)
      }
      this.recognition.onaudioend = () => {
        console.log('音声認識が終了')
      }
      this.recognition.onspeechend = () => {
        console.log('音声の検出終了')
      }
    },
    startSpeech () {
      if (this.started) {
        this.recognition.stop()
        console.log('Stop')
      } else {
        this.recognition.start()
        console.log('Start')
      }
      this.started = !this.started
    },
    appendtext (val) {
      document.getElementById('totext').value += val + '\n'
      document.getElementById('totext').scrollTop = 99999
    },
    save () {
      const blob = new Blob([this.speech], { type: 'text/plain' })
      const link = document.createElement('a')
      link.href = window.URL.createObjectURL(blob)
      link.download = 'entwurf.txt'
      link.click()
    },
    clear () {
      this.speech = ''; this.proc = ''
    }
  }
}
</script>

<style lang="sass" scoped>
.speech
  width: 800px
</style>
