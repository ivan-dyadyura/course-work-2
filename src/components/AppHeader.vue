<template>
  <div class="editable" :class="{'editable-active': isEdit}">
    <div class="edit-block" v-if="isEdit">
      <button class="btn primary">Изменить</button>
      <button class="btn danger" @click="$emit('remove')">Удалить</button>
    </div>
    <h1>
      {{ value }}
      <button class="edit-element" @click="isEdit = true">
        <img src="../assets/pen.svg" alt="">
      </button>
    </h1>
  </div>
</template>

<script>
export default {
  name: 'AppHeader',
  emits: ['remove'],
  props: {
    value: String
  },
  data() {
    return {
      isEdit: false,
    }
  },
}
</script>

<style scoped>

</style>
