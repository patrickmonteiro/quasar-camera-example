<template>
  <q-page padding>
    <p class="text-h6">Camera</p>
    <div class="row">
      <div class="col-12 text-center">
        <video autoplay width="250rem" ref="videoplay"></video>
      </div>
      <div class="col-12 text-center">
        <q-btn
          v-if="!cameraStart"
          label="Acessar Camera"
          color="primary"
          icon="camera"
          :disable="!enableCamera"
          @click="useCamera"
        />
        <q-btn
          v-else
          label="Tirar Foto"
          color="primary"
          icon="camera"
          @click="takePhoto"
        />
      </div>
      <div class="col-12 text-center">
        <img src="" ref="imgTakePhoto" width="250rem" />
      </div>
    </div>
  </q-page>
</template>

<script>
export default {
  name: 'PageCamera',
  data () {
    return {
      enableCamera: false,
      cameraStart: false,
      imageCapture: null,
      track: null
    }
  },
  mounted () {
    if (navigator.mediaDevices.getUserMedia) {
      this.enableCamera = true
    }
  },
  methods: {
    useCamera () {
      navigator.mediaDevices.getUserMedia({ video: true })
        .then(mediaStream => {
          this.cameraStart = true
          this.$refs.videoplay.srcObject = mediaStream
          this.track = mediaStream.getVideoTracks()[0]
          this.imageCapture = new ImageCapture(this.track)
        })
    },
    takePhoto () {
      this.imageCapture.takePhoto()
        .then(blob => {
          createImageBitmap(blob)
          const reader = new FileReader()
          reader.readAsDataURL(blob)
          reader.onloadend = () => {
            this.$refs.imgTakePhoto.src = reader.result
            console.log(reader.result)
          }
        })
        .catch(error => console.log(error))
    }
  }
}
</script>
