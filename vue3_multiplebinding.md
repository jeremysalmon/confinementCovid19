## Multibinding avec Vue3

On peut maintenant créer des composant avec plusieurs v-model, ce qui facilite grandement la mise à jour du composant "mère" ... Plutot que de faire des $emit pour récupérer les valeurs, il suffit simplement de mettre plusieurs v-model au composant.

### Le composant 

```
<template>
	<div>
	  <select name="salutation">
	    <option value="">-</option>
	     <option
	      v-for="item of salutations"
	      :value="item"
	      :key="item"
	      :selected="salutation === item"
	    >
	      {{ item }}
	    </option>
	  </select>
	
	  <input
	    :value="name"
	    type="text"
	    name="name"
	  />
	</div>
</template>
```

```
<script>
const salutations = [
  'Ms.',
  'Mrs.',
  'Miss',
  'Mx.',
  'Dr.'
]

export default {
  props: {
    salutation: {
      type: String,
      default: ''
    },
    name: {
      type: String,
      default: ''
    }
  },
  setup () {
    return {
      salutations
    }
  }
}
</script>
```

### L'application

```
<template>
  <div id="app">
    <SalutationName
      v-model:salutation="form.salutation"
      v-model:name="form.name"
    />

    <pre>{{ form }}</pre>
  </div>
</template>

<script>
import { reactive } from 'vue'
import SalutationName from './components/SalutationName'

export default {
  name: 'App',
  components: {
    SalutationName
  },
  setup () {
    const form = reactive({
      salutation: '',
      name: ''
    })

    return {
      form
    }
  }
}
</script>
```

Les deux v-model correspondent aux props du composant. Pas besoin de recevoir des $events ou autre pour mettre à jour les propriétés.

On utilise la nouvelle méthode ```setup()``` et on importe ```reactive``` pour pouvoir créer des propriétés reactive ...

