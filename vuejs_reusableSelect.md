### Objectif : créer un select réutilisable

On va créer un ```src/components/BaseSelect.vue```

```
<template>
  <div>
    <label v-if="label">{{ label }}</label>
    <select
      :value="value"
      v-bind="$attrs"
      @change="updateValue"
    >
      <option v-for="option in options"
        :selected="option === value"
        :value="option"
        :key="option"
        {{ option }}
      >
      </option>
    </select>
  </div>
</template>
<script>
export default {
  inheritAttrs: false,
  props: {
    options: {
      type: Array,
      required: true
    }
    value: [String, Number]
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

Dans nos components on va pouvoir utiliser : 

```
<BaseSelect
  v-model="reclamation.category"
  label="Selectionnez une catégorie"
  :options:"categories"
/>
```

Si on a des besoins plus complexes en termes de select : [vue-multiselect](https://vue-multiselect.js.org/)
