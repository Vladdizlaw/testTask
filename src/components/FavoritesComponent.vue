<template>
  <div class="wrapper_fav">
    <div class="empty_wrapper" v-if="favorites.length == 0">
      <p>Нет избранного</p>
    </div>
    <div class="photo_wrapper" v-if="favorites.length > 0">
      <div class="photo_row">
        <div
          class="photo_thumb"
          v-for="(photo, index) in favorites"
          :key="index"
        >
          <img
            width="150"
            height="150"
            :src="photo.thumbnailUrl"
            :title="photo.title"
          />
          <div class="photo_title">
            <p>{{ photo.title }}</p>
          </div>

          <div @click="removeFromFavorites(photo)" class="image_star">
            <img
              v-if="favorites.includes(photo)"
              src="../assets/yellow_star.png"
              alt=""
            />
            <img
              v-if="!favorites.includes(photo)"
              src="../assets/star.svg"
              alt=""
            />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: "FavoritesComponent",
  props: {
    favorites: {
      //избранное
      type: Array,
      required: false,
    },
  },
  data() {
    return {};
  },
  methods: {
    removeFromFavorites(id) {
      //Удаление из избранного
      const updatedfavorites = this.favorites.filter((photo) => photo !== id);
      this.$emit("favorites", { favorites: updatedfavorites });
    },
  },
};
</script>
<style scoped>
.wrapper_fav {
  width: 100%;
  height: 90%;
  display: flex;
  top: 5vh;
  justify-content: center;
  align-items: start;
  overflow-y: auto;
}
.empty_wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 2rem;
}
.photo_wrapper {
  width: 500px;
  display: flex;
  align-items: start;
  flex-direction: column;
  flex-wrap: wrap;
  gap: 1rem;
  overflow-y: auto;
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
  flex-direction: column;
  width: 150px;
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
.photo_title {
  display: flex;
  width: 100%;
  overflow-wrap: break-word;
  justify-content: center;
}
.image_star > img {
  width: 100%;
}
</style>
