<template>
  <div class="wrapper-catalog">
    <div class="catalog-users">
      <div
        class="catalog-users_user"
        v-for="(user, index) in users"
        :key="index"
      >
        <div class="user_title">
          <div
            :ref="user.id"
            class="icon_add"
            @click="
              openAlbum = !openAlbum;
              showAlbum(user.id);
            "
          ></div>
          <p>{{ user.name }}</p>
        </div>
        <div
          class="album_wrapper"
          v-if="albums && openAlbum && user.id == albums[0].userId"
        >
          <div
            class="catalog-users_user_album"
            v-for="(album, index) in albums"
            :key="index"
          >
            <div class="user_title">
              <div
                :ref="album.id"
                class="icon_add"
                @click="
                  openPhoto = !openPhoto;
                  showPhoto(album.id);
                "
              ></div>
              <p>{{ album.title }}</p>
            </div>
            <div
              class="photo_wrapper"
              v-if="photos && openPhoto && album.id == photos[0].albumId"
            >
              <div class="photo_row">
                <div
                  class="photo_thumb"
                  v-for="(photo, index) in photos"
                  :key="index"
                >
                  <img
                    width="150"
                    height="150"
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
              <show-modal ref="modal" :url="url"/>
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
      type: Array,
      required: false,
    },
   favorites: {
      type: Array,
      required: false,
    },
  },
  data() {
    return {
      albums: null,
      openAlbum: false,
      openedUser: null,
      photos: null,
      openPhoto: false,
      // favorites: [],
      url: null,
    };
  },
  computed:{
    favoritesId(){
      const newFavorites= this.favorites.map(el=>{
        return el.id
      })
      console.log(newFavorites)
      return newFavorites
    }
  },
  methods: {
    openModal(url) {
      this.url = url;
      this.$refs.modal[0].openModal();
    },
    async getAlbumsData(id) {
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
      try {
        let data = await fetch(
          `http://jsonplaceholder.typicode.com/photos?albumId=${id}`,
          {
            method: "GET",
          }
        );
        data = await data.json();
        this.photos = data;
        console.log(this.photos);
      } catch (e) {
        console.log(e);
      }
    },
    addToFavorites(photo) {
      if (!this.favoritesId.includes(photo.id)) {
        let newfavorites=this.favorites
        newfavorites.push(photo)
          console.log('not inc;uded')
        this.$emit("favorites",{favorites:newfavorites})
      } else {
       
       let newfavorites = this.favorites.filter((el) => el.id !== photo.id);
       console.log('inc;uded')
       this.$emit("favorites",{favorites:newfavorites})
      }
      
      console.log(this.favorites);
    },
    changeIconColor(id) {
      if (this.openAlbum) {
        if (this.openedUser) {
          this.$refs[this.openedUser][0].style.backgroundColor = "blue";
          this.openedUser = null;
        }
        this.$refs[id][0].style.backgroundColor = "yellow";
      } else if (!this.openAlbum) {
        this.$refs[id][0].style.backgroundColor = "blue";

        if (this.openedUser) {
          this.$refs[this.openedUser][0].style.backgroundColor = "blue";
          console.log("yellow", this.openedUser);
          this.openedUser = null;
        }
      }
    },
    async showAlbum(id) {
      await this.getAlbumsData(id);
      this.changeIconColor(id);
      this.openedUser = id;
    },
    async showPhoto(id) {
      await this.getPhotoData(id);
      this.changeIconColor(id);
      this.openedAlbum = id;
    },
  },
  mounted() {
    console.log('prps',this.$props)
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

  height: 100%;
  width: 100%;
  display: flex;
  justify-content: start;
  align-items: start;
  flex-direction: column;
  overflow-y: auto;

  /* gap:1rem; */
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
  width: 1.5rem;
  height: 1.5rem;
  border-radius: 50%;
  position: relative;
  background-color: blue;
}
.icon_add::before {
  content: "";
  width: 0;
  height: 40%;
  top: 50%;
  border: 2px solid white;
  left: 40%;
  position: absolute;
  transform: translateY(-50%);
}
.icon_add::after {
  content: "";
  width: 40%;
  height: 0;
  top: 40%;
  border: 2px solid white;
  left: 50%;
  position: absolute;
  transform: translateX(-50%);
}
.album_wrapper {
  margin-left: 2rem;
  display: flex;
  justify-content: space-around;
  align-items: start;
  flex-direction: column;
}
.photo_wrapper {
  width: 500px;
  display: flex;
  justify-content: space-around;
  align-items: start;
  flex-direction: column;
  flex-wrap: wrap;
  gap: 1rem;
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
