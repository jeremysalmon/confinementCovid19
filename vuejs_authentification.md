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
