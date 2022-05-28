<script lang="ts" setup>

function handleClick() {
  window.scrollTo({
    top: 0,
  })
}

const { y } = useScroll(typeof document === 'undefined' ? null : document)

</script>

<template>
  <transition>
    <div
      v-if="y > 20"
      class="w-10 h-10 fixed bottom-10 right-10 rd-50% flex"
      shadow="~ black"
      bg="white"
      cursor="pointer"
      items-center
      justify-center
      @click.stop="handleClick"
    >
      <div i-carbon:arrow-up text-6 color="teal-700" />
    </div>
  </transition>
</template>

<style>
.v-enter-active,
.v-leave-active {
  transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}
</style>
