
<template>
  <div id="app">
    <div class="container">
      <h1 class="title">Gif Searcher</h1>
      <link
        rel="stylesheet"
        href="https://cdn.materialdesignicons.com/2.5.94/css/materialdesignicons.min.css"
      />

      <div class="search-container">
        <b-field label="Search terme">
          <b-input placeholder="Search..." v-model="searchTerm" rounded icon-clickable></b-input>
        </b-field>
        <b-field label="How many?">
          <b-numberinput
            controls-position="compact"
            v-model="limit"
            type="is-dark"
            min="0"
            controls-rounded
          ></b-numberinput>
        </b-field>
        <b-button icon-left="magnify" type="is-dark" @click="getGifs">Search</b-button>
      </div>
      <div class="gif-container">
        <img v-for="gif in gifs" :src="gif" :key="gif.id" alt @click="handleImgClick" class="gif" />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  components: {},
  data() {
    return {
      searchTerm: "",
      limit: 0,
      gifs: {}
    };
  },
  methods: {
    getGifs() {
      let apiKey = "dc6zaTOxFJmzC"; //giphy public api key
      let searchEndPoint = "https://api.giphy.com/v1/gifs/search?";

      let url = `${searchEndPoint}&api_key=${apiKey}&q=${
        this.searchTerm
      }&limit=${this.limit ? this.limit : 5}`;

      this.axios.get(url).then(response => {
        this.buildGifs(response.data);
      });
    },
    buildGifs(json) {
      this.gifs = json.data
        .map(gif => gif.id)
        .map(gifId => {
          return `https://media.giphy.com/media/${gifId}/giphy.gif`;
        });
    },
    handleImgClick() {
      var link = event.srcElement.src;
      try {
        this.copyStringToClipboard(link);
        this.$buefy.toast.open({
          message: "Copied to clipboard",
          type: "is-success"
        });
      } catch (err) {
        this.$buefy.toast.open({
          message: "Oops, unable to copy",
          type: "is-danger"
        });
      }
    },
    copyStringToClipboard(str) {
      // Create new element
      var el = document.createElement("textarea");
      // Set value (string to be copied)
      el.value = str;
      // Set non-editable to avoid focus and move outside of view
      el.setAttribute("readonly", "");
      el.style = { position: "absolute", left: "-9999px" };
      document.body.appendChild(el);
      // Select text inside element
      el.select();
      // Copy text to clipboard
      document.execCommand("copy");
      // Remove temporary element
      document.body.removeChild(el);
    }
  }
};
</script>
<style>
@import url("https://fonts.googleapis.com/css?family=Fredoka+One&display=swap");
#app {
  text-align: center;
  margin-top: 40px;
}
.container {
  display: flex;
  flex-wrap: nowrap;
  flex-direction: column;
  justify-content: center;
}
.search-container {
  width: 250px;
  height: auto;
  margin: 0 auto 10px auto;
}
.title {
  font-family: "Fredoka One", sans-serif;
  font-size: 2rem;
}
.gif-container {
  display: flex;
  flex-wrap: wrap;
  flex-direction: row;
  justify-content: center;
}
.gif {
  cursor: pointer;
  width: 25vw;
  height: auto;
}
</style>