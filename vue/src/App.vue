<script setup>
import {onBeforeMount, ref} from 'vue';
import { helperNameMap } from '@vue/compiler-core';

const merpRss = 'https://tech.lgbt/@solarmerps.rss';
const shitPosts = ref({});
const soffImage = ref('');
const user = ref('');

onBeforeMount(async () => {
  const superGay = await fetch(merpRss).then(response => response.text())
    .then(str => new window.DOMParser().parseFromString(str, "text/xml"));
  if (superGay.querySelector('image') && superGay.querySelector('image').querySelector('url')) {
    soffImage.value = superGay.querySelector('image').querySelector('url').textContent;
    user.value = superGay.querySelector('channel>title').textContent;
  }
  
  shitPosts.value = [...superGay.querySelectorAll('item')].map(gayPost => {
      const tempNode = document.createElement('div');
      const shitPost = {};
      tempNode.innerHTML = gayPost.querySelector('description').textContent;
      shitPost.content = tempNode.innerHTML;
      const c = tempNode.textContent.toLowerCase();
      shitPost.hasGay = ((c.includes('im') || c.includes("i'm")) && c.includes('gay')) || (!c.includes('not') && !c.includes('you') && c.includes('gay'));
      // TODO: Change to none and set up timer.
      shitPost.display = 'block';
      return shitPost;
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
  <section class="container">
    <div class="row">
      <div class="col">
        <h1>{{user}}</h1>
      </div>
    </div>
  </section>
  <div :style="{ backgroundImage: `url('${soffImage}')` }" class="container">
    <section class="row row-cols-2 g-5">
      <div v-for="gayPost in shitPosts" :class="gayPost.hasGay ? 'pretty-gay col' : 'less-gay col'"
           :style="{display: gayPost.display}">
        <div class="card p-3">
          <h4 class="card-title" v-html="gayPost.content"></h4>
        </div>
      </div>
    </section>
  </div>
</template>   

<style scoped>

</style>
