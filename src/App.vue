<template>
  <app-alert :alert="alert" @close="alert = null"></app-alert>
  <div class="container column">
    <form class="card card-w30" @submit.prevent="addContent">
      <div class="form-control">
        <label for="type">Тип блока</label>
        <select id="type" v-model="select">
          <option value="header">Заголовок</option>
          <option value="subtitle">Подзаголовок</option>
          <option value="avatar">Аватар</option>
          <option value="text">Текст</option>
        </select>
      </div>

      <div class="form-control">
        <label for="value">Значение</label>
        <textarea id="value" v-model="value" rows="3"></textarea>
      </div>

      <button
          class="btn primary"
          :disabled="!isValid || isSending"
      >Добавить
      </button>
    </form>

    <div class="card card-w70">
      <div class="loader" v-if="loading && !contentModel.length"></div>
      <template v-else>
        <template v-if="contentModel.length">
          <component
              v-for="item in contentModel"
              :is="item.type"
              :value="item.value"
              :key="item.id"
              @remove="removeElement(item.id)"
          >
          </component>
        </template>
        <p v-else>
          Нет созданных элементов, вы можете сделать это на панели слева.
        </p>
      </template>

    </div>
  </div>
  <div class="container">
    <p>
      <button class="btn primary" @click="loadComments">Загрузить комментарии</button>
    </p>

    <div class="loader" v-if="loadingComment && commentsArray.length === 0"></div>
    <div class="card" v-else-if="commentsArray.length !== 0">
      <h2>Комментарии</h2>
      <ul class="list">
        <li class="list-item" v-for="comment in commentsArray">
          <div>
            <p><strong>{{ comment.email }}</strong></p>
            <small>{{ comment.text }}</small>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>


<script>
import AppHeader from '@/components/AppHeader'
import AppAvatar from "@/components/AppAvatar"
import AppSubtitle from "@/components/AppSubtitle"
import AppAlert from "@/components/AppAlert"
import AppText from "@/components/AppText"

export default {
  data() {
    return {
      select: 'header',
      contentModel: [],
      value: '',
      minLength: 3,
      alert: null,
      isSending: false,
      loading: false,
      commentsArray: [],
      loadingComment: false
    }
  },
  mounted() {
    this.loadContent()
  },
  methods: {
    async addContent() {
      this.isSending = true
      this.loading = true
      try {
        const response = await fetch('https://resume-generator-55e7a-default-rtdb.firebaseio.com/content.json', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({
            type: this.componentType,
            value: this.value
          })
        })

        const firebaseData = await response.json()

        this.contentModel.push({
          value: this.value,
          type: this.componentType,
          id: firebaseData
        })
        this.value = ''
        this.isSending = false
        this.loading = false
      } catch (e) {
        console.log(e.message)
        this.isSending = false
        this.loading = false
      }
    },
    async loadContent() {
      this.isSending = true
      this.loading = true
      try {
        const data = await fetch('https://resume-generator-55e7a-default-rtdb.firebaseio.com/content.json')
            .then(response => response.json())

        this.contentModel = Object.keys(data).map(key => {
          return {
            id: key,
            ...data[key]
          }
        })
        this.isSending = false
        this.loading = false
      } catch (e) {
        this.isSending = false
        this.loading = false
      }
    },
    async removeElement(id) {
      this.isSending = true
      this.loading = true
      try {
        const type = this.contentModel.find(content => content.id === id).value
        const response = await fetch(`https://resume-generator-55e7a-default-rtdb.firebaseio.com/content/${id}.json`, {
          method: 'DELETE',
        })
        this.contentModel = this.contentModel.filter(content => content.id !== id)
        this.alert = {
          type: 'primary',
          title: 'Успешно',
          text: `${type} был удален`
        }
        this.isSending = false
        this.loading = false
      } catch (e) {
        this.alert = {
          type: 'danger',
          title: 'Пользователь не удален по неизвестной причине',
          text: e.message
        }
      }
    },
    async loadComments() {
      try {
        this.loadingComment = true
        const data = await fetch('https://jsonplaceholder.typicode.com/comments?_limit=42')
            .then(response => response.json())

        this.commentsArray = Object.keys(data).map(key => {
          return {
            ...data[key]
          }
        })
        this.loadingComment = false
      } catch (e) {
      }
    }
  },
  computed: {
    componentType() {
      return 'app-' + this.select
    },
    isValid() {
      return this.value.length >= this.minLength
    }
  },
  components: {
    AppHeader, AppAvatar, AppSubtitle,
    AppText, AppAlert
  }
}
</script>

<style>
.avatar {
  display: flex;
  justify-content: center;
}

.avatar img {
  width: 250px;
  height: 250px;
  border-radius: 50%;
  object-fit: cover;
}
</style>
