<script setup>
import { computed, defineProps, defineEmits } from 'vue'
import IconUp from './icons/IconUp.vue'
import IconDown from './icons/IconDown.vue'
import IconPicture from './icons/IconPicture.vue'

const props = defineProps({
  post: {
    type: Object,
    Required: true,
  },
})
defineEmits(['downvote', 'upvote'])

function getRGB(c) {
  return parseInt(c, 16) || c
}

function getsRGB(c) {
  return getRGB(c) / 255 <= 0.03928
    ? getRGB(c) / 255 / 12.92
    : Math.pow((getRGB(c) / 255 + 0.055) / 1.055, 2.4)
}

function getLuminance(hexColor) {
  return (
    0.2126 * getsRGB(hexColor.substr(1, 2)) +
    0.7152 * getsRGB(hexColor.substr(3, 2)) +
    0.0722 * getsRGB(hexColor.substr(-2))
  )
}

function getContrast(f, b) {
  const L1 = getLuminance(f)
  const L2 = getLuminance(b)
  return (Math.max(L1, L2) + 0.05) / (Math.min(L1, L2) + 0.05)
}

function getTextColor(bgColor) {
  const whiteContrast = getContrast(bgColor, '#ffffff')
  const blackContrast = getContrast(bgColor, '#000000')

  return whiteContrast > blackContrast ? 'text-white' : 'text-black'
}

const IconColor = computed(() => getTextColor(props.post.color))
const url = computed(() => new URL(props.post.submitted_by.img, import.meta.url))
</script>

<template>
  <div class="flex flex-row min-h-16 justify-between items-center">
    <div class="img h-full align-middle w-[3/20] me-6">
      <div :style="`background-color: ${post.color}9f`" class="h-100 w-100 p-8">
        <IconPicture :class="'opacity-80 ' + IconColor" />
      </div>
    </div>
    <div class="content text-left self-start w-full">
      <a :href="post.url" class="text-blue-500 text-[20px]">
        {{ post.title }}
        <span class="bg-gray-100 text-gray-800 text-[15px] px-[10px]">#{{ post.id }}</span>
      </a>
      <p class="text-gray-700 mb-3 text-[16px]">{{ post.details }}</p>

      <div class="submition_details">
        <p class="text-[12px] text-gray-500">
          Submitted By: <img :src="url" :alt="post.submitted_by.name" />
        </p>
      </div>
    </div>
    <div class="votes ms-6">
      <IconUp
        :class="{
          'text-black': !post.voted,
          'text-blue-500': post.voted && post.vote === 'up',
          'cursor-pointer': true,
        }"
        @click="$emit('upvote', post.id)"
      />
      <i class="my-2 inline-block">{{ post.votes }}</i>
      <IconDown
        :class="{
          'text-black': !post.voted,
          'text-blue-500': post.voted && post.vote === 'down',
          'cursor-pointer': true,
        }"
        @click="$emit('downvote', post.id)"
      />
    </div>
  </div>
</template>

<style lang="scss" scoped></style>
