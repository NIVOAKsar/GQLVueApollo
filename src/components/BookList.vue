<template>
  <div class="book-list">
    <div class="book book-list-item" v-for="book in books" :key="book.id">
      <p class="book-title">Title: {{book.title}}</p>
      <p class="book-author">Author: {{book.author}}</p>
      <!-- <p class="book-id">id: {{book.id}}</p> -->
    </div>
    <button @click="addBook">ADD BOOK</button>
    <!-- <button @click="removeBook(5)">REMOVE BOOK OF 5</button> -->
  </div>
</template>

<script>
import gql from 'graphql-tag'

export default {
  name: 'BookList',

  data: () => ({
    books: []
  }),
  apollo: {
    // Simple query that will update the 'books' vue property
    // books: gql`query {
    //   books {
    //     id
    //     title
    //     author
    //   }
    // }`,
  },
  created() {
    this.loadBooks()
  },
  methods: {
    async loadBooks() {
      const fetch = this.$apollo.provider.defaultClient.query
      const query = gql`
            query {
              books {
                id
                title
                author
              }
            }
        `;
      const options = { query }
      try {
        const { data } = await fetch(options)
        this.books = data.books.map(book => ({
          id: book.id,
          title: book.title,
          author: book.author
        }))
      }
      catch (error) {
        console.log(error)
      }
    },
    async addBook() {
      const mutation = gql`
        mutation ($title: String, $author: String) {
          addBook(title: $title, author: $author){
            title,
            author
          }
        }
      `
      const variables = {
        title: "Fox in Socks",
        author: "Dr. Seuss"
      }
      const options = { mutation, variables }
      try {
        const { data } = await this.$apollo.mutate(options)
        console.log(data);

        const id = data.addBook.id
        const title = data.addBook.title
        const author = data.addBook.author
        const book = { id, title, author }
        this.books.push(book)
      }
      catch (error) {
        console.log(error)
      }
    },
    async removeBook(id) {
      const mutation = gql`
        mutation ($id: String) {
          removeBook(id: $id){
            id
          }
        }
      `
      const variables = {
        id: 5
      }
      const options = { mutation, variables }
      try {
        const { data } = await this.$apollo.mutate(options)
        const idx = this.books.findIndex(book => book.id === id)
        this.books.splice(idx, 1)
      }
      catch (error) {
        console.log(error)
      }
    }
  }
}
</script>

<style scoped>
.book-list-item {
  padding: 5px;
  border-radius: 10px;
  border: 1px black solid;
}
.book-list-item:not(:last-child) {
  margin-bottom: 10px;
}

p {
  font-family: sans-serif;
}

p:not(:last-child) {
  margin-bottom: 5px;
}

.book-id {
  font-size: 12px;
}

.book-title {
  font-size: 14px;
  font-weight: bold;
}

.book-author {
  font-size: 12px;
}
</style>
