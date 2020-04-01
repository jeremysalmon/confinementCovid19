## Comment gérer les erreurs 404 proprement avec vue-router, vue et Axios

### Un template pour les erreurs 404

/src/views/Error404.vue

```
<template>
  <div>
    <h1>Oops!</h1>
    <h3>La page 
    <template v-if="resource">
      {{ resource }}
    </template>
     est introuvable</h3>
    <router-link :to="{ name: 'accueil' }">Retourner à l'accueil</router-link>
  </div>  
</template>

<script>
export default () {
  props: {
    resource : {
      type: String,
      required: true
    }
  }
}
</script>
```

### Un template pour les autres erreurs (ex : erreur réseau)

/src/views/ErrorOther.vue

```
<template>
  <div>
    <h1>Oops!</h1>
    <h3>Etes vous connecté au réseau ?</h3>
    <router-link :to="{ name: 'accueil' }">Retourner à l'accueil</router-link>
  </div>  
</template>
```

### On va créer les routes (voir router.js)

```
import Error404 from './views/Error404.vue'
import ErrorOther from './view/ErrorOther.vue'

routes: [
        ...
        { // Ici la route qui recupere une info d'une API par exemple
          path: '/reclamation/:id',
          name: 'reclamation-show',
          component: ReclamationShow,
          props: true,
          beforeEnter(routeTo, routeFrom, next) {
            store
              .dispatch('reclamation/fetchEvent', routeTo.params.id)
              .then(reclamation => {
                routeTo.params.reclamation = reclamation
                next()
              })
              .catch(error => {
                if (error.response && error.response.status == 404) {
                  next({
                    name: '404',
                    params: { resource: 'reclamation' }
                  })
                } else {
                  next({ name: 'error-other' })
                }
              })
        },  
        {
          path: '/404',
          name: '404',
          component: Error404,
          props: true // Converti les params en PROPS dans le component
        },
        {
          path: '/error-other',
          name: 'error-other',
          component: ErrorOther,
          props: true // Converti les params en PROPS dans le component
        },
        
        {
          path: '*',
          redirect: { name: '404', params: { ressource: 'page' } }
        }
      ]
```

### Timeout dans Axios

Maintenant il ne faut pas oublier de mettre ```timeout: 15000``` dans les paramètres des requêtes Axios


