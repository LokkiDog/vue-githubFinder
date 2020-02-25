<template>
  <div class="wrapper-content wrapper-content--fixed">
    <section>
      <div class="container"> 

        <!-- search -->
        <search 
          :value="search"
          placeholder="Имя пользователя Github"
          @search="search = $event"/>

        <button class="btn btnPrimary" @click="getRepo">Search</button>

        <!-- wrapper -->
        <div class="repos__wrapper" v-show="repos">
          <!-- title -->
          <div class="repos__title">
            <span>Название</span>
            <span>Изменялся</span>
          </div>
          <!-- item -->
          <div class="repos__info" v-for="repo in repos" :key="repo.id">
            <div class="repos__item">
              <a class="link" target="_blank" :href="repo.html_url">{{ repo.name}}</a>
            </div>
            <span class="repo__stars"> {{ show(repo.updated_at) }} 🕒</span>
          </div>
        </div>

        <p class="error" v-show="error">{{ error }}</p>

      </div>
    </section>
  </div>
</template>
 
<script>
import search from '@/components/Search.vue'
import axios from 'axios'

export default {
  components: { search },
  data() {
    return {
      search: '',
      repos: null,
      error: null
    }
  },
  methods: {
    getRepo() {
      axios
        .get(`https://api.github.com/users/${this.search}/repos`)
          .then(res => {
            this.repos = res.data
            this.error = null
          })
          .catch(err => {
            this.error = "Такой пользователь не найден!"
            this.repos = null
          })
    },
    show(time) {
      let mas = time.split("T")[0].split("-");
      return mas[2]+'.'+mas[1]+'.'+mas[0]
    }
  }
  
}
</script>

<style lang="scss" scoped>
.container {
  display: flex;
  align-items: center;
  flex-direction: column;
}
button {
  margin-top: 40px;
}
.repos { 
  &__wrapper {
    width: 400px;
    margin: 30px 0;
  }
  &__info {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 10px;
    padding: 10px 0;
    border-bottom: 1px solid #dbdbdb;
  }
  &__title {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 10px;
    padding: 10px 0;
    border-bottom: 1px solid #dbdbdb;
    font-weight: bold;
  }
}
.error {
  margin-top: 30px;
  text-align: center;
  color: rgb(236, 37, 37)
}

</style>