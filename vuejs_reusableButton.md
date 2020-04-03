### Objectif : créer un button réutilisable

Ici nous allons voir comment rendre dynamique les classes, car dans Vue 2 $attrs ne prend pas en charge les classes et les styles.

On va créer un ```src/components/BaseButton.vue```

```
<template>
  <div>
    <button v-on="$listeners" :class="buttonClass> // On hérite tous les events listener
      <slot/>
    </button>
  </div>
</template>

<script>
export default {
  inheritAttrs: false,
  props: {
    buttonClass: {
      type: String
    }
  }
}
</script>
```

Pour utiliser : 
```<BaseButton type="submit" buttonClass="-fill-gradient">Submit</BaseButton>```


