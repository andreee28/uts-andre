<template>
  <body>
  <!-- NavBar -->
  <nav class="navbar navbar-expand-lg navbar-dark bg-dark shadow fixed-top">
    <div class="container">
      <div class="collapse navbar-collapse" id="navbarNav">
        <ul class="navbar-nav ms-auto">
        </ul>
      </div>
    </div>
  </nav>
  <!-- Akhir NavBar -->
  </body>

  <section class="search">
    <input
        type="number"
        v-model="inputnomor"
        class="input"
        placeholder="Masukkan nomor surah"
    />
    <button @click="lihat" class="btn">OK</button>
  </section>

  <section class="surah">
    <div class="judull">
      <h1 v-if="namaSurah" class="judul">{{ namaSurah.name_simple }}</h1>
    </div>
  </section>

  <section class="hasil">
    <div class="hasill">
      <ul class="lista">
        <li class="ayat" v-for="ayat in ayah" :key="ayat.id">
          {{ ayat.text_uthmani }} {{ ayat.text }}
        </li>
      </ul>
    </div>
  </section>
</template>
<script>
import axios from "axios";

export default {
  data() {
    return {
      ayah: [],
      audio: null,
      namaSurah: null,
      inputnomor: "",
    };
  },

  methods: {
    async lihat() {
      let nomor = this.inputnomor;
      let ayat = `https://api.quran.com/api/v4/quran/verses/uthmani?chapter_number=${nomor}`;
      let arti =
          "https://api.quran.com/api/v4/quran/translations/134?chapter_number=" +
          nomor;

      let judul = "https://api.quran.com/api/v4/chapters?language=en";
      let suara =
          "https://api.quran.com/api/v4/chapter_recitations/2?language=en";

      if (nomor <= 0 || nomor > 114) {
        alert("tidak terdeteksi");
      } else {
        const reqAyat = axios.get(ayat);
        const reqArti = axios.get(arti);
        const reqJudul = axios.get(judul);
        const reqSuara = axios.get(suara);

        axios.all([reqAyat, reqArti, reqJudul, reqSuara]).then(
            axios.spread((...response) => {
              const responseAyat = response[0];
              const responseArti = response[1];
              const responseJudul = response[2];
              const responseSuara = response[3];

              const a = responseAyat.data.verses;
              const b = responseArti.data.translations;

              const gabung = (a, b) => {
                const res = [];

                for (let i = 0; i < a.length + b.length; i++) {
                  if (i % 2 === 0) {
                    res.push(a[i / 2]);
                  } else {
                    res.push(b[(i - 1) / 2]);
                  }
                }
                return res;
              };

              this.ayah = gabung(a, b);
              this.audio =
                  responseSuara.data.audio_files[nomor - 1];
              this.namaSurah =
                  responseJudul.data.chapters[nomor - 1];
            })
        );
      }
    },
  },
};
</script>
<style>
body{
  background: #e2dfdf;
}
.search {
  display: flex;
  margin: 20px 0 0 0;
  justify-content: left;
}

.input {
  height: 50px;
  border: 1px solid #000000;
  color: rgb(0, 0, 0);
}

.input:hover {
  border: 1px solid #000000;
}

.btn {
  background-color: #73c962;
  border: 1px solid #000000;
  height: 54px ;
  font-size: 12px;
  color: black;
  border-radius: 1%;
}

.btn:hover {
  color: rgb(255, 255, 255);
}

.judul {
  text-align: center;
  font-size: 65px;
  color: #323232;
  margin: 50px 0 0 30px;
}

.ayat {
  color: #323232;
  list-style: none;
  margin: 0 250px 50px 200px;
}

.ayat:nth-child(odd) {
  text-align: right;
  font-size: 40px;
}
.ayat:nth-child(even) {
  text-align: left;
  font-size: 15px;
  color: #323232;
}

@media screen and (max-width: 400px) {
  .ayat:nth-child(odd) {
    font-size: 20px;
    margin-bottom: 20px;
    margin-right: 10px;
  }
  .ayat:nth-child(even) {
    font-size: 10px;
    margin: 20px;
  }
  .ayat {
    margin: 0;
  }
}
</style>
