<template>
  <template v-if="content.length">
    <component
        v-for="item in content"
        :is="item.type"
        :value="item.value"
        :key="item.id"
    >
    </component>
  </template>
  <p v-else>

  </p>
</template>

<script>
export default {
  name: "AppRenderContent",
  props: ['content'],
}
</script>

<style scoped>

</style>