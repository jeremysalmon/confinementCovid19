### Objectif : créer un input text réutilisable

On va créer un fichier ```src/components/BaseInput.vue```

```
<template>
  <div>
    <label v-if="label">{{ label }}</label>
    <input 
      :value="value"
      @input="updateValue"
      v-bind="$attrs" // Tous les attributs passés seront mis sur le input et pas sur le DIV racine
  </div>
</template>

<script>
export default {
  inheritAttrs: false, // Par defaut c'est l'élément racine qui prend les attrs. (donc le div dans notre cas). Ceci va permettre de pouvoir attribuer $attrs a ce qu'on veut
  props: {
    label: String
  },
  methods: {
    updateValue(event) {
      this.$emit('input', event.target.value)
    }
  }
}
</script>
```

### Pour utiliser notre BaseInput

```
<BaseInput
  label="Title"
  v-model="reclamation.title"
  type="text"
  placeholder="Add an reclamation title"
/>
```
