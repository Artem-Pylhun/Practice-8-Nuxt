<script setup lang="ts">

const links = [{
  label: 'Posts',
  to: '/'
}
]
</script>

<template>
  <UHorizontalNavigation :links="links">
    <template #default="{ link }">
      <span class="group-hover:text-primary relative">{{ link.label }}</span>
    </template>
  </UHorizontalNavigation>
  <router-view/>
</template>


<style scoped>

</style>