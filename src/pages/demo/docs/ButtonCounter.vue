<script lang="ts" setup>
const count = ref(0)

function clicked(): void {
}
</script>

<template>
  <button @click="clicked">
    You clicked me {{ count }} times.
  </button>
</template>

<style lang="scss" scoped>
// .el-alert {
//   margin-bottom: 20px;
// }
</style>
