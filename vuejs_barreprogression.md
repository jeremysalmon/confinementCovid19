### Utilisation de nProgress

L'install avec ```npm install --save nprogress``` 

Importer le CSS dans ```main.js```

```import 'nprogress/nprogress.css'```

On va implémenter le déclenchement et la suppression de nProgress dans le router avant et après chaque route : 

```
router.beforeEach((routeTo, routeFrom, next) => {
  // On démarre la barre de progression
  NProgress.start()
  next()
})
router.afterEach(() => {
  // On arrête la barre de progression
  NProgress.done()
})
```

### Fetcher les données avant l'arrivée dans la page 

Dans la route : 

```
{
  path: '/reclamation/:id',
  name: 'reclamation-show',
  component: ReclamationShow,
  props: true,
  beforeEnter(routeTo, routeFrom, next) { // avant que la route ne soit chargée
    store.dispatch('reclamation/fetchReclamation', routeTo.params.id).then(() => {
      next()
    })
  }
}
```

Dans le ```fetchReclamation``` du store on doit retourner la promise du getReclamation 

```
fetchReclamation({ commit, getters, dispatch }, id) {
  var reclamation = getters.getReclamationById(id)
  if (reclamation) {
    commit('SET_RECLAMATION', reclamation)
  } else {
    return ReclamationService.getReclamation(id) // <--- On ajoute le retour de la Promise 
      .then(response => {
        commit('SET_RECLAMATION', response.data)
      })
      ...
```

Et dans le component qui affiche la réclamation on va faire une ```computed``` propertie

```
import { mapState } from 'vuex'
    
export default {
  props: ['id'],
  computed: mapState({
    reclamation: state => state.reclamation.reclamation
  })
}
```
