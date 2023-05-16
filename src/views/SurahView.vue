<template>
    <li v-for="(item,index) in daftarsurah">
    {{item.name_simple+" (" + item.name_arabic + ")"}}
      <p v-html = " daftarsurah[index].verses_count + ' ayat ' "></p>
      <router-link :to="{name: 'surahdetail', params: {id: item.id}}" class="btn btn-primary mb-4">baca</router-link>
      <hr>
    </li>
  </template>
  
  <script>
  import {ref} from "vue";
  import axios from "axios";
  export default {
      data() {
        return {
          daftarsurah: ref([])
        }
      },
      mounted() {
        this.getDaftarSurah()
      },
      methods: {
        getDaftarSurah() {
          axios.get("https://api.quran.com/api/v4/chapters?language=id")
              .then((respons) =>
              {
               console.log(respons)
                this.daftarsurah = respons.data.chapters
              })
              .catch((err) =>
              {
                console.log('error' + err)
              })
        }
      }
    }
  
  
  </script>
  
  <style scoped>
  .btn-primary.mb-4{
    color: rgb(0, 0, 0);
    font-size: 15px;
    padding: 5px;

  }
  li {
    text-align: right;

  }
  
  
  </style>