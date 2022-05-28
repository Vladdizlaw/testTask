<template>
  <div id="app">
    <div class="wrapper">
      <div class="header">
        <div class="header-left" @click="currentComponent = 'CatalogComponent'">
          Каталог
        </div>
        <div
          class="header-right"
          @click="currentComponent = 'FavoritesComponent'"
        >
          <img
            src="./assets/star.png"
            v-if="currentComponent !== 'FavoritesComponent'"
            alt=""
          />
          <img
            src="./assets/blue_star.png"
            v-if="currentComponent == 'FavoritesComponent'"
            alt=""
          />
          Избранное
        </div>
      </div>
      <div class="main">
        <keep-alive>
          <catalog-component
            :users="users"
            :favorites.sync="favorites"
            v-if="currentComponent == 'CatalogComponent'"
            @favorites="changeFavorites"
          />
          <favorites-component
            :favorites.sync="favorites"
            v-if="currentComponent == 'FavoritesComponent'"
            @favorites="changeFavorites"
          />
        </keep-alive>
      </div>
    </div>
  </div>
</template>

<script>
import CatalogComponent from "./components/CatalogComponent.vue";
import FavoritesComponent from "./components/FavoritesComponent.vue";
export default {
  name: "App",
  components: { CatalogComponent, FavoritesComponent },
  data() {
    return {
      currentComponent: "CatalogComponent", //текущий компонент
      users: [], //массив загружаемых пользователей
      favorites: [], //избраное
    };
  },
  methods: {
    changeFavorites(e) {
      //изменеие избранного и запись в локал сторадж
      this.favorites = e.favorites;
      window.localStorage.setItem("favorites", JSON.stringify(this.favorites));
    },
    async getUsersData() {
      //получение данных
      try {
        let data = await fetch("http://jsonplaceholder.typicode.com/users", {
          method: "GET",
        });
        data = await data.json();
        this.users = data;
      } catch (e) {
        console.log(e);
      }
    },
  },
  async mounted() {
    await this.getUsersData();
    const favorites = window.localStorage.getItem("favorites");
    favorites ? (this.favorites = JSON.parse(favorites)) : null;
  },
};
</script>

<style scoped>
#app {
  margin: 0px;
  padding: 0px;
  user-select: none;
  box-sizing: border-box;
  height: 100vh;
  width: 100vw;
  overflow: hidden;
}

.wrapper {
  display: flex;
  flex-direction: column;
  height: 100vh;
  width: 100vw;
  overflow: hidden;
  position: relative;
}
.header {
  display: flex;
  flex-direction: row;
  justify-content: space-around;
  align-items: center;
  width: 100%;
  height: 5%;
  font-size: 2rem;
}
.header-left {
  display: flex;
  justify-content: center;
  align-items: center;
  border-right: 1px solid grey;
  width: 50%;
  height: 100%;
}
.header-right {
  display: flex;
  justify-content: center;
  align-items: center;
  border-left: 1px solid grey;
  width: 50%;
  height: 100%;
}
.header-right > img {
  height: 1.5rem;
}
.main {
  width: 100%;
  height: 95%;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
