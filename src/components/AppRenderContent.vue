<template>
  <template v-if="dat.length">
    <component
        v-for="item in dat"
        :is="item.type"
        :value="item.value"
        :key="item.id"
        @remove="test(item.id)"
    >
    </component>
  </template>
  <p v-else>
    Нет созданных элементов, вы можете сделать это на панели слева.
  </p>
</template>

<script>
import AppHeader from '@/components/AppHeader'
import AppAvatar from "@/components/AppAvatar"
import AppSubtitle from "@/components/AppSubtitle"
import AppText from "@/components/AppText"

export default {
  name: "AppRenderContent",
  data() {
    return {
      dat: this.content
    }
  },
  props: ['content'],
  emits: ['remove'],
  methods: {
    test(data) {
      this.$emit('remove', data)
    }
  },
  components: {
    AppAvatar, AppHeader, AppSubtitle, AppText
  }
}
</script>

<style scoped>

</style>