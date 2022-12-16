<script setup>
import {onBeforeMount, ref} from 'vue';
import { helperNameMap } from '@vue/compiler-core';

const merpRss = 'https://tech.lgbt/@solarmerps.rss';
const shitPosts = ref({});
const soffImage = ref('');

onBeforeMount(async () => {
  const superGay = await fetch(merpRss).then(response => response.text())
    .then(str => new window.DOMParser().parseFromString(str, "text/xml"));
  if (superGay.querySelector('image') && superGay.querySelector('image').querySelector('url')) {
    soffImage.value = superGay.querySelector('image').querySelector('url').textContent;
  }
  
  shitPosts.value = [...superGay.querySelectorAll('item')].map(gayPost => {
      const tempNode = document.createElement('div');
      tempNode.innerHTML = gayPost.querySelector('description').textContent;
      console.log(tempNode);
      return tempNode.textContent;
    });
    return {shitPosts};
});

const messages = {
  scanning: [
    'Scanning for signs of gay'
  ],
  yes: [
    'Gay detected. Continuing scan.',
    'Large amounts of gay detected. Continuing scan.',
    'Massive amounts of gay detected. Continuing scan. For science!',
    'Excessive amounts of gay detected. Scan aborted.'
  ],
  no: [
    'Seeking signs of gay.',
    'No gay detected. Yet. Further research necessary.',
    'Still no gay. Having doubts about initial hypothesis.',
    "Hang on. Something isn't right here. Sample contains no gay. "
  ],
  paradox: [
    'Paradox detected. This statement is a lie.'
  ]
}

</script>

<template>
  <div :style="{ backgroundImage: `url('${soffImage}')` }" class="container">
    <section class="row row-col-3" v-for="gayPost in shitPosts">
      <div class="card">
        <h3 class="card-title">{{ gayPost }}</h3></div>
    </section>
  </div>
</template>   

<style scoped>

</style>
