<template>
  <div class="wrapper-content wrapper-content--fixed">
    <section>
      <div class="container"> 

        <!-- user__wrapper -->
        <div class="user__wrapper" v-if="user">
          <div class="user__avatar">
            <img class="user__img" :src="user.avatar_url" alt="Avatar">
          </div>
          <div class="user__info">
            <p class="user__login">Логин: {{ user.login }}</p>
            <p class="user__name">Имя: {{ user.name }}</p>
            <p class="user__count-repos">Количество репозиториев: {{ user.public_repos }}</p>
          </div>
        </div>
        <!-- /.user__wrapper -->


        <!-- search__wrapper -->
        <div class="search__wrapper">

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
        <!-- /.search__wrapper -->
        
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
      user: null,
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
            
            this.getUserInfo(this.search)
          })
          .catch(err => {
            this.error = "Такой пользователь не найден!"
            this.user = null
            this.repos = null
          })
    },
    getUserInfo(user) {
      axios
        .get('https://api.github.com/users/' + user)
          .then(res => {
            this.user = res.data
            this.error = null

          })
          .catch(err => {
            this.error = "Такой пользователь не найден!"
            this.user = null
            
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
.search__wrapper {
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



// User
.user {
  &__wrapper {
    display: flex;
    align-items: center;
    margin: 30px 0;
  } 
  &__info{
    display: flex;
    flex-direction: column;
    align-items: flex-start;
  }
  &__avatar{
    border-radius: 50%;
    max-width: 150px;
    overflow: hidden;
    margin-right: 20px;
  }
  &__img {
  }
  &__login{
    margin-top: 15px;
  }
  &__name{
    margin-top: 15px;
  }
  &__count-repos{
    margin-top: 15px;
  }
}
</style>