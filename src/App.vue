<template>
  <div id="app">
   
    <div class="wrapper">
      <div class="header">
        <div class="header-left" :class="{active:(currentComponent == 'CatalogComponent')}" @click="currentComponent = 'CatalogComponent'">
          Каталог
        </div>
        <div
          class="header-right" :class="{active:(currentComponent == 'FavoritesComponent')}"
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
    //обнуление css
    const htmlEl = document.querySelector("html");
    htmlEl.style.overflow = "auto";
    const body = document.querySelector("body");
    body.style.margin = 0;
    //Запрос пользователей и проверка с взятием избранного в локал сторадже
    await this.getUsersData();
    const favorites = window.localStorage.getItem("favorites");
    favorites ? (this.favorites = JSON.parse(favorites)) : null;
  },
};
</script>

<style scoped>

#app {
  display:flex;
  justify-content: center;
  align-items: center;
  margin: 0px;
  user-select: none;
  box-sizing: border-box;
  min-height: 100vh;
  width: 100vw;
  overflow: auto; 
  background-color: rgb(155, 220, 236);
  padding-top:1rem;
  padding-bottom: 1rem;
}

.wrapper {
  display: flex;
  flex-direction: column;
  min-height: 80vh;
  width: 40%;
  overflow: hidden;
  position: relative;
  background-color: rgb(245, 250, 250);
  border-radius:0.5rem;

 

}
.wrapper ::-webkit-scrollbar {
  width: 0;
  background: transparent;
}
.header {
  display: flex;
  flex-direction: row;
  justify-content:start;
  align-items: center;
  width: 100%;
  height: auto;
  font-size: max(1.3vw,1.2rem);
   background-color: rgb(202, 206, 207);
   gap:0.1rem;
}
.header-left {
  display: flex;
  justify-content: center;
  align-items: center;
  justify-self: start;
  border-left: 1px solid rgb(202, 206, 207);
  border-top: 1px solid rgb(202, 206, 207);
  width: 49.3%;
  height: 100%;
  border-radius: 5px 5px 0 0;
  padding:0.5rem 0 0.5rem 0;
}
.header-right {
  justify-self: end;
  display: flex;
  justify-content: center;
  align-items: center;
  /* border-left: 1px solid grey; */
  width: 50.4%;
  height: 100%;
  border-radius: 5px 5px 0 0;
  padding:0.5rem 0 0.5rem 0;
}
.header-right > img {
  height: 1.5rem;
}
.main {
  width: 100%;
  height: 90%;
  display: flex;
  justify-content: center;
  align-items: center;
}
.active{
  background-color: rgb(245, 250, 250);
  border-left: 1px solid rgb(245, 250, 250);
  border-top: 1px solid rgb(245, 250, 250);
}

</style>
