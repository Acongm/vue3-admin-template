<script lang="ts" setup>
const count = ref(0)
watchPostEffect(() => {
  console.log("watchPostEffect", count.value)
})
watch(
  () => count.value,
  (count1, prevCount) => {
    console.log("watch count", count1)
    console.log("watch prevCount", prevCount)
  }
)

function handleAdd() {
  count.value++
}
</script>

<template>
  <div pa-20px h-full text-center flex select-none all:transition-400>
    <div ma>
      <div text-5xl fw100 animate-bounce-alt animate-count-infinite animate-duration-1s @click="handleAdd">
        UnoCSS
      </div>
      {{ count }}
      <div op30 text-lg fw300 m1 dark:op60>
        该页面是一个 UnoCSS 的使用案例，其他页面依旧采用 Scss
      </div>
      <div m2 flex-x-center text-lg op30 hover="op80" dark:op60 dark:hover="op80">
        <a href="https://antfu.me/posts/reimagine-atomic-css-zh" target="_blank">
          推荐阅读：重新构想原子化 CSS
        </a>
      </div>
    </div>
  </div>
</template>
