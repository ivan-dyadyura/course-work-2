<template>
  <div class="avatar">
    <img :src="value">
  </div>
</template>

<script>
export default {
  name: "AppAvatar",
  props: {
    value: String
  }
}
</script>

<style scoped>

</style>
