## Custom modifier 

Vue2 permettait déjà d'utiliser des ```modifier``` sur les ```v-model```
Nous avions a notre disposition ```lazy``` qui permettait d'écouter change plutot que input, ```number``` qui castait le valeur en integer et ```trim``` qui supprimait les espaces.

Avec Vue3 on peut créer ses propres ```modifier``` et ainsi traiter en temps réel les différentes propriétés.

## Dans le composant mère

```
<template>
  <div id="app">
    <SalutationName
      v-model:salutation.capitalize="form.salutation"
      v-model:name.capitalize="form.name"
    />

    <pre>{{ form }}</pre>
  </div>
</template>
```

### Dans le composant 

```
[...]
props: {
  salutation: {
    type: String,
    default: ''
  },
  salutationModifiers: {
    default: () => ({}),
    type: Object
  },
  name: {
    type: String,
    default: ''
  },
  nameModifiers: {
    default: () => ({}),
    type: Object
  }
},
[...]
```

Les ```...Modifiers``` renvoit un objet. Quand on met des modifiers dans le v-model alors l'objet contient des propriétés à true. Par exemple dans notre cas on aurait {capitalize:true}

Dans les composants utilisés dans la page mère on va ajouter des @change, @input ... sur lesquels on va appeler les méthodes qui nous permettront d'appliquer d'appliquer nos modifiers.

La méthode ```setup()``` de notre composant sera transformé : 
```
[...]
setup (props, { emit }) {
  const updateSalutation = event => {
    let val = event.target.value
    if (props.salutationModifiers.capitalize) {
      val = val.toUpperCase()
    }

    emit('update:salutation', val)
  }

  const updateName = event => {
    let val = event.target.value
    if (props.nameModifiers.capitalize) {
      val = val.charAt(0).toUpperCase() + val.slice(1)
    }

    emit('update:name', val)
  }

  return {
    salutations,
    updateSalutation,
    updateName
  }
}
[...]
```

On a ajouté des arguments à ```setup()```
On teste les objets ```...Modifiers``` pour voir leurs propriétés. Voir l'exemple de capitalize.

