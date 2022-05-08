<script>
import { ref } from "vue";
import axios from "axios";

export default {
  data()
  {
    return {
      cari: '1',
      surah: ref([]),
      judul: ref([]),
      name: [],
      audio: ref([]),
    }
  },

  watch: {
    cari()
    {
      this.getSurah()
      this.getJudul()
      this.getAudio()
    }
  },

  mounted()
  {
    this.getSurah()
    this.getJudul()
    this.getAudio()
  },

  methods: {
    async getSurah() {
      await axios.get('https://api.quran.com/api/v4/quran/verses/uthmani?chapter_number=' + this.cari)
        .then(response =>
        {
          this.surah = response.data.verses
        })
        .catch(error =>
        {
          console.log(error)
        })
        .finally(() => this.loading = false)
    },
    async getJudul() {
      await axios.get('https://api.quran.com/api/v4/chapters/' + this.cari + '?language=id')
        .then(response =>
        {
          this.judul = response.data.chapter
          this.name = this.judul.translated_name
        })
        .catch(error =>
        {
          console.log(error)
        })
        .finally(() => this.loading = false)
    },
    async getAudio() {
      await axios.get('https://api.quran.com/api/v4/chapter_recitations/7/' + this.cari )
        .then(response =>
        {
          this.audio = response.data.audio_file
        })
        .catch(error =>
        {
          console.log(error)
        })
        .finally(() => this.loading = false)
    }
  }
}
</script>

<template>
  <div class="text-center">
    <h1>Masukkan nomor surah!</h1>
    <input v-model="cari" class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
    <div class="mt-5">
      <h1>{{ judul.name_complex }}</h1>
      <br>
      <h1>{{ name.name }}</h1>
      <br>
      <h2>Diturunkan di {{ judul.revelation_place }}</h2>
    </div>
    <div v-if ="audio">
      <audio v-bind:src="audio.audio_url" controls></audio>
    </div>
    <div v-for="ayat in surah" :key="ayat.id" class="mt-5">
      {{ ayat.text_uthmani }}
    </div>
  </div>
</template>
