<template>
  <transition name="bounce">
    <div class="modal" @click="cancelModal()" v-show="isOpen">
      <div ref="content" class="content">
       <img v-if="url" :src="url" alt="">
      </div>
    </div>
  </transition>
</template>
<script>
export default {
  name: "ShowModal",
  props: { url: String },
  data() {
    return {
      isOpen: false,
    
    };
  },
  modalController: null,
  methods: {
    openModal() {
      let resolve;
      let reject;
     
    console.log(this.url)
      const promiseModal = new Promise((ok, cancel) => {
        resolve = ok;
        reject = cancel;
      });
      this.$options.modalController = { resolve, reject };
      this.isOpen = true;
      // console.log(this.$options.modalController)
      return promiseModal;
    },
    cancelModal() {
      
        this.$options.modalController.resolve(false);
        this.isOpen = false;
      
    },
    
    
    cancelEsc(e) {
      if (e.key == "Escape") {
        this.$options.modalController?.resolve(false);
        this.isOpen = false;
      }
    },
    confirm() {
      // console.log('modal confirm')
      this.$options.modalController.resolve(true);
      this.isOpen = false;
    },
  },
  mounted() {
    document.addEventListener("keydown", this.cancelEsc);
  },
  beforeDestroy() {
    document.removeEventListener("keydown", this.cancelEsc);
  },
};
</script>
<style  scoped>
.modal {
  user-select: none;
  position: fixed;
  background: rgba(235, 222, 42, 0.863),
    linear-gradient(to bottom, rgba(245, 246, 252, 0.52));
  backdrop-filter: blur(2px);
  width: 100vw;
  height: 100vh;
  transition: all 1s;
  top:0;
  left:0;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.content {
  transition: all 1s;
  padding: 10px 10px 10px 10px; 
  height: 100vh;
  width: 100vw;
  display: flex;
  justify-content:center;
  align-items: center;
  overflow: hidden;
  border-radius: 10px;
  will-change: auto;
 /* z-index: 1001; */
  transition: all 0.4s;
  box-shadow: 5px 6px 4px rgba(0, 0, 0, 0.25), 0px 4px 4px rgba(0, 0, 0, 0.25),
    0px 4px 4px rgba(0, 0, 0, 0.25);
   
  }
  .content>img{
      width:600px;
      height:600px;
  }


.bounce-enter-active {
  animation: bounce-in 0.5s;
}

@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
</style>