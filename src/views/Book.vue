<template>
  <div class="about">
    <BookManager></BookManager>
  </div>
</template>

<script>
// @ is an alias to /src
import BookManager from '@/components/BookManager.vue'

export default {
  name: 'book',
  components: {
    BookManager
  }
}
</script>