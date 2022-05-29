<template>
  <div class="wrapper-catalog">
    <div class="catalog-users">
      <div
        class="catalog-users_user"
        v-for="(user, index) in users"
        :key="index"
      >
        <div class="user_title" @click="showAlbum(user.id)">
          <div class="icon_add">
            <img
              v-if="!openAlbums || user.id !== openedUser"
              src="../assets/closed_circle.svg"
              alt=""
            />
            <img
              v-if="openAlbums && user.id == openedUser"
              src="../assets/opened_circle.svg"
              alt=""
            />
          </div>
          <p>{{ user.name }}</p>
        </div>
        <div
          class="album_wrapper"
          v-if="albums && openAlbums && user.id == albums[0].userId"
        >
          <div
            class="catalog-users_user_album"
            v-for="(album, index) in albums"
            :key="index"
          >
            <div class="user_title" @click="showPhoto(album.id)">
              <div class="icon_add">
                <img
                  v-if="!openPhotos || (openAlbums && album.id !== openedAlbum)"
                  src="../assets/closed_circle.svg"
                  alt=""
                />
                <img
                  v-if="openPhotos && openAlbums && album.id == openedAlbum"
                  src="../assets/opened_circle.svg"
                  alt=""
                />
              </div>
              <p>{{ album.title }}</p>
            </div>
            <div
              class="photo_wrapper"
              v-if="photos && openPhotos && album.id == photos[0].albumId"
            >
              <div class="photo_row">
                <div
                  class="photo_thumb"
                  v-for="(photo, index) in photos"
                  :key="index"
                >
                  <img
                    class="photo_thumb_image"
                    @click="openModal(photo.url)"
                    :src="photo.thumbnailUrl"
                    :title="photo.title"
                  />
                  <div @click="addToFavorites(photo)" class="image_star">
                    <img
                      v-if="favoritesId.includes(photo.id)"
                      src="../assets/yellow_star.png"
                      alt=""
                    />
                    <img
                      v-if="!favoritesId.includes(photo.id)"
                      src="../assets/star.svg"
                      alt=""
                    />
                  </div>
                </div>
              </div>
              <show-modal ref="modal" :url="url" />
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import ShowModal from "../components/ShowModal.vue";
export default {
  components: { ShowModal },
  name: "CatalogComponent",
  props: {
    users: {
      //пользователи
      type: Array,
      required: false,
    },
    favorites: {
      //избраное
      type: Array,
      required: false,
    },
  },
  data() {
    return {
      albums: null, //Массив загружаемых альбомов
      openAlbums: false, //флаг показывающий открыт ли список альбомов
      openedAlbum: null, //текущий открытый альбом
      openedUser: null, //текущий открытый пользователь
      photos: null, //Массив загружаемых фото
      openPhotos: false, //флаг показывающий открыт ли список фотографий
      url: null, //урл передаваемый в модалку
    };
  },
  computed: {
    favoritesId() {
      //делает массив id фотографий  из избраного(props)

      return this.favorites.map((el) => {
        return el.id;
      });
    },
  },
  methods: {
    openModal(url) {
      //открывает модалку
      this.url = url;
      this.$refs.modal[0].openModal();
    },
    async getAlbumsData(id) {
      //получает альбомы и записывает в albums
      try {
        let data = await fetch(
          `http://jsonplaceholder.typicode.com/albums?userId=${id}`,
          {
            method: "GET",
          }
        );
        data = await data.json();
        this.albums = data;
      } catch (e) {
        console.log(e);
      }
    },
    async getPhotoData(id) {
      //получает фото записывает в photos
      try {
        let data = await fetch(
          `http://jsonplaceholder.typicode.com/photos?albumId=${id}`,
          {
            method: "GET",
          }
        );
        data = await data.json();
        this.photos = data;
      } catch (e) {
        console.log(e);
      }
    },
    addToFavorites(photo) {
      //добавляет\удаляет из избранного
      if (!this.favoritesId.includes(photo.id)) {
        let newfavorites = [...this.favorites];
        newfavorites.push(photo);
        this.$emit("favorites", { favorites: newfavorites });
      } else {
        let newfavorites = this.favorites.filter((el) => el.id !== photo.id);
        this.$emit("favorites", { favorites: newfavorites });
      }
    },
    async showAlbum(id) {
      //закрытие открытие и смена иконок альбомов
      await this.getAlbumsData(id);
      if (this.openedUser == id) {
        this.openAlbums = false;
        this.openedUser = null;
      } else {
        this.openAlbums = true;
        this.openedUser = id;
      }
    },
    async showPhoto(id) {
      //закрытие открытие и смена иконок фото
      await this.getPhotoData(id);
      if (this.openedAlbum == id) {
        this.openPhotos = false;
        this.openedAlbum = null;
      } else {
        this.openPhotos = true;
        this.openedAlbum = id;
      }
    },
  },
};
</script>
<style scoped>
.wrapper-catalog {
  height: 100%;
  width: 100%;
  display: flex;
}
.catalog-users {
  margin-left: 1rem;
  padding: 1rem 1rem 1rem 1rem;
  height: 100%;
  width: 100%;
  display: flex;
  justify-content: start;
  align-items: start;
  flex-direction: column;
  overflow-y: auto;
}
.catalog-users_user {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  gap: 0.3rem;
  font-size: max(1vw, 1rem);
}
.user_title {
  display: flex;
  width: 100%;
  flex-direction: row;
  justify-content: start;
  align-items: center;
  gap: 0.5rem;
}
.icon_add {
  width: 1rem;
  height: 1rem;

  position: relative;
}
.icon_add > img {
  width: 1rem;
  height: 1rem;
}
.album_wrapper {
  padding-left: 2rem;

  display: flex;
  justify-content: space-around;
  align-items: start;
  flex-direction: column;
}
.photo_wrapper {
  padding-left: 2rem;
  width: 100%;
  display: flex;
  justify-content: space-around;
  align-items: center;
  flex-direction: column;
  flex-wrap: wrap;
  gap: 1rem;
}
@media screen and(orientati) {
}
.photo_row {
  width: 100%;
  display: flex;
  justify-content: start;
  align-items: start;
  flex-direction: row;
  flex-wrap: wrap;
  gap: 15px;
}
.photo_thumb {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
}
.photo_thumb_image {
  width: clamp(9vw, 150px, 4rem);
}
.image_star {
  position: absolute;
  width: 1rem;
  height: 1rem;
  border-radius: 50%;
  background: white;
  top: 10px;
  right: 10px;
}
.image_star > img {
  width: 100%;
}
</style>
