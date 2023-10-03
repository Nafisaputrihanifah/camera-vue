<template>

  <div>
    <nav class="bg-gray-800 p-4 text-white">
      <div class="container mx-auto">
        <h1 class="text-2xl font-semibold">Aplikasi Kamera</h1>
        <ul class="mt-4">
          <li>
          </li>
          </ul>
          </div>
          </nav>
    <center>
      <button
        v-if="cameraOpen === false"
        @click="bukaKamera"
        class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded" style="margin-top: 20px; margin-bottom: 10px;"
      >
        Buka Kamera
      </button>
      <button
        v-else
        @click="tutupKamera"
        class="bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded" style="margin: 20px;"
      >
        Tutup Kamera
      </button>

      <div>
        <img v-if="ulangis === false" :src="imgSrc" />
        <img v-else :src="imgSrc" /><br />
        <video ref="videoElement" autoplay></video>

        <button
          v-if="cameraOpen === true"
          @click="ambilFoto"
          class="bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded" 
          style="margin-right: 10px;"
        >
          Ambil Foto
        </button>

        <button
          v-if="cameraOpen === true"
          @click="ulangi"
          class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
          style="margin: 20px;"
        >
          Ulangi
        </button>
      </div>

    </center>
  </div>
</template>

<script>

export default {
  
  data() {
    return { stream: null, imgSrc: null, cameraOpen: false, ulangis: false };
  },
  methods: {
    bukaKamera() {
      this.imgSrc = null;
      this.cameraOpen = true;
      navigator.mediaDevices
        .getUserMedia({ video: true })
        .then((stream) => {
          this.stream = stream;
          this.$refs.videoElement.srcObject = stream;
        })
        .catch((error) => {
          console.error("Error accessing camera:", error);
        });
    },
    tutupKamera() {
      if (this.stream) {
        this.stream.getTracks().forEach((track) => track.stop());
        this.$refs.videoElement.srcObject = null;
      }
      this.cameraOpen = false;
    },
    ambilFoto() {
      const video = this.$refs.videoElement;
      const canvas = document.createElement("canvas");
      canvas.width = video.videoWidth;
      canvas.height = video.videoHeight;
      const context = canvas.getContext("2d");
      context.drawImage(video, 0, 0, canvas.width, canvas.height);
      const dataURL = canvas.toDataURL("image/png");
      this.imgSrc = dataURL;
      // Tidak perlu menutup kamera di sini
      // Mengatur ulangis menjadi true
      this.ulangis = true;
      this.tutupKamera();
    },
    ulangi() {
      this.imgSrc = null;
      // Menutup kamera saat tombol "Ulangi" diklik
      this.tutupKamera();
      this.bukaKamera(); // Membuka kembali kamera
      // Mengatur ulangis menjadi true
      this.ulangis = true;
    },
  },
  mounted() {
    // Panggil bukaKamera saat komponen dimuat pertama kali
    this.bukaKamera();
  },
};
</script>
