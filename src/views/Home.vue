<template>
  <div class="home">
    <BookList />
  </div>
</template>

<script>
// @ is an alias to /src
import BookList from '@/components/BookList.vue'

export default {
  name: 'Home',
  components: {
    BookList
  }
}
</script>
