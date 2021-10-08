<template>
  <div class="container">
    <div class="row">
      <div class="col">
        <Checklist :id="1"/>
      </div>
    </div>
  </div>
</template>

<script>
import Checklist from '@/components/CheckList.vue'

export default {
  name: 'Checklist View',
  
  components: {
    Checklist,
  },
};
</script>

<style scoped>

</style>
