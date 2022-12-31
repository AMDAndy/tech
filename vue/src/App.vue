<script setup>
import {onBeforeMount, ref} from 'vue';
import { helperNameMap } from '@vue/compiler-core';

const merpRss = 'https://tech.lgbt/@solarmerps.rss';
const shitPosts = ref({});
const soffImage = ref('');
const user = ref('');
const message = ref('');
const paradox = ref(false);
const gay = ref(false);
const classe = ref('alert-warning');

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
      shitPost.display = 'none';
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
  ],
  gay: [
    'User confirmed gay.'
  ]
}

// go through each item, analyze for gayness, increment gayness and analyzed counts.
// delay between each item, update messaging as applicable.
// when done, if no gayness is detected, show big error message.
// if gayness is detected, show that gay was detected.

// This is our timer. Wait for it to be done between each item.
const timer = ms => new Promise(res => setTimeout(res, ms))

const updateMessage = (count, gayCount) => {
  let mess = '';
  if (gayCount == 0) {
    mess = messages.no.length >= count ? messages.no[count] : messages.no[messages.no.length - 1];
  }
  else {
    if (messages.yes.length > gayCount) {
      mess = messages.yes[gayCount - 1];
    }
    else {
      mess = messages.yes[messages.yes.length - 1];
    }
  }
  message.value = mess;
}
async function examinePosts () {
  let gayCount = 0;
  const posts = [...shitPosts.value];
  for (let i = 0; i < posts.length; i++) {
    const post = posts[i];
    post.display = post.hasGay ? 'block' : 'none';
    if (post.hasGay) {
      // Increment gay. Comment out to test zero gay actions.
      gayCount++;
      classe.value = 'alert-success';
    }
    updateMessage(i, gayCount);
    await timer(500); // then the created Promise can be awaited
  }
  if (gayCount == 0) {
    message.value = messages.paradox[0];
    paradox.value = true;
    classe.value = 'alert-error';
  }
  else {
    message.value = messages.gay[0];
    gay.value = true;
    classe.value = 'alert-gay';
  }
}


// initial delay to run the thing.
setTimeout(examinePosts, 500);

</script>

<template>
  <section class="container">
    <div class="row">
      <div class="col">
        <h1>{{user}}</h1>
      </div>
    </div>
  </section>
  <div class="container">
    <section v-if="paradox" class="row paradox">
      <img src="/public/broken.jpg" alt="Zero amounts of gay detected. Something is very wrong here." />
    </section>
    <section class="row">
      <div v-if="message" class="alert" :class="classe" role="alert">{{ message }}</div>
    </section>
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
