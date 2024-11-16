<script setup>
import post from './components/post.vue'
import { computed, ref } from 'vue'

const posts = ref([])
if (localStorage.getItem('posts') === null) {
  posts.value = [
    {
      id: 1,
      url: '#',
      color: '#ffd000',
      title: 'Yellow Pail',
      details: 'on-demand sand castle construction expertise.',
      votes: 16,
      submitted_by: {
        name: 'LovelyOne',
        img: '@/assets/lovely.png',
      },
      voted: true,
      vote: 'down',
    },
    {
      id: 2,
      url: '#',
      color: '#ad0000',
      title: 'Supermajority: The Fantasy Congress League',
      details: 'Earn points when your favorite politicians pass legislation.',
      votes: 11,
      submitted_by: {
        name: 'GloomyOne',
        img: '@/assets/gloomy.png',
      },
      voted: true,
      vote: 'up',
    },
    {
      id: 3,
      url: '#',
      color: '#0084ff',
      title: 'Tinfoild: Tailored tinfoil hats',
      details: 'We Have your measurements and shipping address.',
      votes: 17,
      submitted_by: {
        name: 'Mika',
        img: '@/assets/mika.png',
      },
      voted: false,
      vote: '',
    },
    {
      id: 4,
      url: '#',
      color: '#02a871',
      title: 'Haught or Naught',
      details: 'High-minded or absent-minded? You decide.',
      votes: 9,
      submitted_by: {
        name: 'WonderfulOne',
        img: '@/assets/wonderful.png',
      },
      voted: false,
      vote: '',
    },
  ]
  localStorage.setItem('posts', JSON.stringify(posts.value))
} else {
  posts.value = JSON.parse(localStorage.getItem('posts'))
}

function upVote(id) {
  let post = posts.value.filter((e) => e.id == id)[0]

  if (post.voted) {
    if (post.vote == 'down') {
      post.vote = 'up'
      post.votes += 2
    } else if (post.vote == 'up') {
      post.votes -= 1
      post.vote = ''
      post.voted = false
    }
  } else {
    post.voted = true
    post.vote = 'up'
    post.votes++
  }
  localStorage.setItem('posts', JSON.stringify(posts.value))
}
function downVote(id) {
  let post = posts.value.filter((e) => e.id == id)[0]

  if (post.voted) {
    if (post.vote == 'up') {
      post.vote = 'down'
      post.votes -= 2
    } else if (post.vote == 'down') {
      post.votes += 1
      post.vote = ''
      post.voted = false
    }
  } else {
    post.voted = true
    post.vote = 'down'
    post.votes--
  }
  localStorage.setItem('posts', JSON.stringify(posts.value))
}

const postss = computed(() => posts.value.sort((a, b) => b.votes - a.votes))
</script>

<template>
  <div class="page text-center mt-14">
    <h1 class="text-[50px] mb-20">UpVote!</h1>

    <div class="container mx-auto">
      <div
        class="post border-2 rounded py-[20px] px-[25px] my-10 hover:border-blue-300"
        v-for="data in postss"
        :key="data.id"
      >
        <post :post="data" @downvote="downVote" @upvote="upVote" />
      </div>
    </div>
  </div>
</template>

<style scoped></style>
