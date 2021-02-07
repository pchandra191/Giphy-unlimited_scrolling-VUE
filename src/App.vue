<template>
  <div>
    <div class="header">
      V-Giphy
      <div class="search box">
        <input
          type="text"
          name="search_key"
          v-on:keyup.enter="Search"
          @keyup.enter="Search"
          v-model="search_key"
        />
        <button @click="Search" v-on:click="Search">Search</button>
      </div>
    </div>

    <div
      id="contents"
      class="images-container"
      :allow-edit-tags="true"
      style="height: auto; width: auto"
      v-on:click="mounted"
    >
      <div class="images-item" v-for="index in images" :key="index">
         <div class="images-card" v-on:click="openurl(`${index.embed_url}`)">
          <img
            class="images-card__image shake"
            v-bind:src="index.images.original.webp"
          />
          <br />
        </div>
      </div>

    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  components: {},
  data() {
    return {
      windowHeight: window.innerHeight,
      offset: 0,
      limit: 3,
      page: 1,
      giphys: [],
      images: [],
      index: [],
      search_key: "",
      ScrollHeight: window.innerHeight,
    };
  },
  methods: {
    handleScroll() {
      if (window.scrollY > this.windowHeight + 100 ) {
        this.windowHeight = this.windowHeight + window.innerHeight / 2;
        this.beforeUpdate();
      }
    },

    Search() {
      this.page = 2;

      this.OnDelete();
      this.beforeUpdate();
    },

    OnDelete(id) {
      this.images = this.images.filter((images) => images.id == id);
    },

    openurl(url) {
      window.open(url);
    },

    beforeUpdate() {
      
      if (this.page > 1 && this.page < 100) {
        axios
          .get("https://api.giphy.com/v1/gifs/search", {
            params: {
              q: this.search_key ? `${this.search_key}` : "happy",
              offset: this.page,
              limit: this.limit,
              api_key: "O0gg2khoeHtxXj4zH7GKjtXCGBDL2TZ0",
            },
          })
          .then((res) => {
            res.data.data && (this.images = [...this.images, ...res.data.data]);
          })
          .catch((err) => console.log("From Error:", err));

        this.page += 3;
      }
      window.addEventListener("scroll", this.handleScroll);
    },
  },
  created() {
    do {
      axios
        .get("https://api.giphy.com/v1/gifs/search", {
          params: {
            q: this.searchkey ? this.searchkey : "happy",
            offset: this.page++,
            limit: 20,
            api_key: "O0gg2khoeHtxXj4zH7GKjtXCGBDL2TZ0",
          },
        })
        .then((res) => {
          this.images = [...this.images, ...res.data.data];
        })
        .catch((err) => console.log("From Error:", err));

      console.log(this.images);
      this.page += 5;
      this.limit += 5;
    } while (this.page == 100);

    window.addEventListener("scroll", this.handleScroll);
  },
};
</script>

<style>
.header {
  background-color: #afc3d8;
  color: #2c3e50;
  margin-top: 6px;
  padding: 1%;
}

.images-container {
  width: 100vw;
  max-width: 1400px;
  padding-bottom: 30px;
  margin: 0 auto;
  display: flex;
  flex-wrap: wrap;
}

.images-item {
  width: 30.333%;
  padding: 1%;
}

.images-card {
  width: 100%;
  height: 0;
  padding-bottom: 70%;
  position: relative;
}
.shake:hover {
  animation: shake 4s cubic-bezier(0.36, 0.07, 0.19, 0.97) both;
  transform: translate3d(10, 30, 10);
  backface-visibility: hidden;
  perspective: 1000px;
}
@keyframes shake {
  10%,
  90% {
    transform: translate3d(-1px, 0, 0);
  }

  20%,
  80% {
    transform: translate3d(2px, 0, 0);
  }

  30%,
  50%,
  70% {
    transform: translate3d(-4px, 0, 0);
  }

  40%,
  60% {
    transform: translate3d(4px, 0, 0);
  }
}
.images-card__image {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  vertical-align: middle;
  display: grid;
  grid-template-columns: repeat(auto-fill, minimum(200px, 1fr));
}
.images-card__mask {
  position: absolute;
  z-index: 9;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  opacity: 0.8;
}
</style>