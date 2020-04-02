### Les routes doivent vérifier le Token

```
app.get('/reclamations', verifyToken, (req, res) => { //verifyToken est un Middleware
jwt.verify(req.token, 'the_secret_key', err => { // verification du token
  if (err) {
    res.sendStatus(401)
  } else {
    res.json({
      reclamations: reclamations
    })
  }
})
})
```

### On va créer un moyen d'enregistrer un nouvel utilisateur

Dans src/views/registerUser.vue

```
 <template>
  <div>
    <form>
      <label for="name">
        Nom:
      </label>
      <input v-model="name" type="text" name="name" value>

      <label for="email">
        Email:
      </label>
      <input v-model="email" type="email" name="email" value>

      <label for="password">
        Mot de passe:
      </label>
      <input v-model="password" type="password" name value>

      <button type="submit" name="button">
        Créer un compte
      </button>
    </form>
  </div>
</template>

<script>
  export default () {
    methods: {
        register () {
          this.$store
            .dispatch('register', {
              name: this.name,
              email: this.email,
              password: this.password
            })
            .then(() => {
              this.$router.push({ name: 'dashboard' })
            })
        }
      }
  }
</script>
```

### On doit configurer un store dans VueX

Dans src/store.js

```
import Vue from 'vue'
import Vuex from 'vuex'
import axios from 'axios'

Vue.use(Vuex)

export default new Vuex.Store({
  state: {
    user: null
  },
  mutations: {
    SET_USER_DATA (state, userData) {
      state.user = userData
      localStorage.setItem('user', JSON.stringify(userData))
      axios.defaults.headers.common['Authorization'] = `Bearer ${
        userData.token
      }`  
    }
  },
  actions: {
    register ({ commit }, credentials) {
      return axios
        .post('//localhost:3000/register', credentials)
        .then(({ data }) => {
          console.log('user data is', userData)
          commit('SET_USER_DATA', data)
        })
     }
  }
})
```

### Création de la route /register dans le router

```
import Vue from 'vue'
import Router from 'vue-router'
import Home from './views/Home.vue'
import Dashboard from './views/Dashboard.vue'
import RegisterUser from './views/RegisterUser.vue'

Vue.use(Router)
const router = new Router({
  mode: 'history',
  base: process.env.BASE_URL,
  routes: [
    {
      path: '/',
      name: 'home',
      component: Home
    },
    {
      path: '/dashboard',
      name: 'dashboard',
      component: Dashboard
    },
    {
      path: '/register',
      name: 'register',
      component: RegisterUser
    }
  ]
})
```

### Un formulaire pour se logguer

Dans src/views/LoginUser.vue

```
<template>
<div>
  <form @submit.prevent="login">
    <label for="email">
      Email:
    </label>
    <input v-model="email" type="email" name="email" value>

    <label for="password">
      Password:
    </label>
    <input v-model="password" type="password" name="password" value>

    <button type="submit" name="button">
      Login
    </button>
  </form>
</div>
</template>

<script>
  export default () {
    data () {
      return {
        email: '',
        password: ''
      }
    },
    methods: {
      login () {
        this.$store
          .dispatch('login', {
            email: this.email,
            password: this.password
          })
          .then(() => { this.$router.push({ name: 'dashboard' }) })
      }
    }
  }
</script>
```

### On ajoute une action dans VueX pour gérer le login : 

```

action {
  ...
  login ({ commit }, credentials) {
    return axios
      .post('//localhost:3000/login', credentials)
      .then(({ data }) => {
        commit('SET_USER_DATA', data)
      })
  }
}
```

### On ajoute un getters dans VueX pour retourner si l'utilistateur est connecté ou non

```
getters: {
  loggedIn (state) {
    return !!state.user
  }
}
```

Le !! permet de retourner l'inverse de la valeur ... et ainsi de savoir s'il est connecté ou non

On va rendre cette valeur accessible à tous les components en créeant ```src/vuex/helpers.js```

```
import { mapGetters } from 'vuex'
    
export const authComputed = {
  ...mapGetters(['loggedIn'])
}
```

Et il suffit d'ajouter dans les components : 

```import { authComputed } from '../vuex/helpers.js'```

Et une valeur computed : 

```
computed: {
  ...authComputed
}
```

La valeur peut maintenant être utilisée dans des ```v-if```

```
<div v-if="!loggedIn">
```

### Se deconnecter avec VueX

On va d'abord créer une ```action``` pour se déconnecter : 

```
logout ({ commit }) {
  commit('LOGOUT')
}
```

Et on ajoute la mutation correspondante : 

```
LOGOUT () {
  localStorage.removeItem('user')
  location.reload()
}
```

Le reload nous permet d'être sur de réinialiser à leurs valeurs par défaut l'ensemble des données du ```state```

### Bloquer les routes qui ont besoin d'authentification

Dans les propriétés des routes qui ont besoin d'une authentification on va ajouter 
```
meta: { requiresAuth: true }
```

Ensuite dans le router avant d'entrer dans 

```
router.beforeEach((to, from, next) => {
  const loggedIn = localStorage.getItem('user')
  if (to.matched.some(record => record.meta.requiresAuth) && !loggedIn) {
    next('/')
  }
  next()
})
```

### Reconnecter l'utilisateur même s'il rafraîchit sa page 

Dans le ```main.js``` au moment de l'instanciation de Vue : 

```
new Vue({
  router,
  store,
  created () {
    const userString = localStorage.getItem('user') // On recupère dans le localstorage l'utilisateur
    if (userString) { // s'il existe
      const userData = JSON.parse(userString) // on converti la chaine en objet
      this.$store.commit('SET_USER_DATA', userData) // et on le stocke dans le VueX
    }
    axios.interceptors.response.use( // On ajoute un interceptors à Axio pour qu'il nous déconnecte en cas d'erreur 401
      response => response,
      error => {
        if (error.response.status === 401) {
          this.$store.dispatch('logout')
        }
        return Promise.reject(error)
      }
    )
  },
  render: h => h(App)
}).$mount('#app')
```
