<template>
  <section class="search section-with-margin">
    <AddBookForm :search="search" :getVolume="getVolume" />
    <div class="search-results" v-if="results">
      <BookSearchItem v-for="(book, i) in results" :key="i" :book="book" />
    </div>
  </section>
</template>

<script>
// import { mapMutations } from "vuex";
// import { defaultVolume, stringFromArrayOfStrings } from "../helpers";
import AddBookForm from "./AddBookForm.vue";
import BookSearchItem from "./BookSearchItem";
const parseString = require('xml2js').parseString;

export default {
  components: {
    AddBookForm,
    BookSearchItem
  },
  data() {
    return {
      results: [],
      search: {
        keyword: "",
        author: ""
      },
      google_books_api: process.env.VUE_APP_GOOGLE_BOOKS_API_KEY,
      goodreads_api: process.env.VUE_APP_GOODREADS_API_KEY
    };
  },
  methods: {
    getVolume() {
      // const keywordUrl = `${this.search.keyword}`;
      // let authorUrl = "";
      // if (this.search.author && !this.search.keyword) {
      //   authorUrl = `inauthor:${this.search.author}`;
      // } else if (this.search.author && this.search.keyword) {
      //   authorUrl = `+inauthor:${this.search.author}`;
      // }
      // const url = `https://www.googleapis.com/books/v1/volumes?q=${
      //   this.search.keyword ? keywordUrl : ""
      // }${this.search.author ? authorUrl : ""}&key=${this.google_books_api}`;
      const url = `https://cors-anywhere.herokuapp.com/https://www.goodreads.com/search/index.xml?key=${this.goodreads_api}&q=Ender%27s+Game`;
      console.log(url);
      this.$http.get(url).then(
        response => {
          parseString(response.data, (err, result) => {
            console.log(result.GoodreadsResponse.search[0].results[0].work);
            this.results = result.GoodreadsResponse.search[0].results[0].work;
          });
        },
        error => {
          console.log(error);
        }
      );
    }
  }
};
</script>

<style lang="scss">
.search {
  .search-results {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    @media (min-width: 768px) {
      justify-content: flex-start;
    }
  }
}
</style>
