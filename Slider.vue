<template>
  <div class="mbatar-vue-slider" @mouseenter="onMouseEnter" @mouseleave="onMouseLeave">
    <transition-group
      :name="currentSlideMode"
      mode="out-in"
      @after-enter="enter"
      @before-leave="before"
    >
      <img
        :src="options.slides[currentSlide].src"
        :key="options.slides[currentSlide].id"
        class="mbatar-vue-slider-slider-image"
      />
      <div
        class="mbatar-vue-slider-description"
        v-if="options.slides[currentSlide].description"
        :key="options.slides[currentSlide].src"
      >
        <h3>{{options.slides[currentSlide].description.header}}</h3>
        <p>
          <a
            :href="options.slides[currentSlide].description.href"
          >{{options.slides[currentSlide].description.title | shortLength(lastChar)}}</a>
        </p>
      </div>
    </transition-group>
    <div class="mbatar-vue-slider-controls">
      <img class="mbatar-vue-slider-control" src="./assets/previous.png" @click="prevSlide" />
      <img class="mbatar-vue-slider-control" src="./assets/next.png" @click="nextSlide" />
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      currentSlide: 0,
      showControl: false,
      sliderInterval: null,
      currentSlideMode: "",
      isOk: "false"
    };
  },
  props: {
    options: {
      required: true,
      type: Object
    }
  },
  filters: {
    shortLength: (val, length) => {
      return `${val.slice(0, length)}...`;
    }
  },
  methods: {
    nextSlide() {
      if (this.currentSlideMode === "mbatar-vue-slider-fade") {
        this.currentSlide++;
      } else {
        this.currentSlideMode = "mbatar-vue-slider-slide-left";
        this.currentSlide++;
      }
    },
    prevSlide() {
      if (this.currentSlideMode === "mbatar-vue-slider-fade") {
        this.currentSlide--;
      } else {
        this.currentSlideMode = "mbatar-vue-slider-slide-right";
        this.currentSlide--;
      }
    },
    onMouseEnter() {
      this.showControl = true;
    },
    onMouseLeave() {
      setTimeout(() => {
        this.showControl = false;
      }, 600);
    },
    enter() {
      this.isOk = true;
    },
    before() {
      this.isOk = false;
    }
  },
  computed: {
    lastChar() {
      return this.options.charLength ? this.options.charLength : 20;
    },
    interval() {
      return this.options.interval ? this.options.interval : 5000;
    }
  },
  created() {
    if (this.options.mode) {
      if (this.options.mode === "slide") {
        this.currentSlideMode = "mbatar-vue-slider-slide-left";
      } else if (this.options.mode === "fade") {
        this.currentSlideMode = "mbatar-vue-slider-fade";
      } else {
        this.currentSlideMode = this.options.mode;
      }
    } else {
      this.currentSlideMode = "mbatar-vue-slider-slide-left";
    }

    if (this.options.auto) {
      this.sliderInterval = setInterval(() => {
        if (!this.showControl) {
          this.currentSlide++;
        }
      }, this.interval);
    }
  },
  beforeDestroy() {
    clearInterval(this.sliderInterval);
  },
  watch: {
    currentSlide(val) {
      val > this.options.slides.length - 1
        ? (this.currentSlide = 0)
        : val < 0
        ? (this.currentSlide = this.options.slides.length - 1)
        : null;
    },
    isOk(val) {
      if (val) {
        if (this.currentSlideMode === "mbatar-vue-slider-slide-right") {
          this.currentSlideMode = "mbatar-vue-slider-slide-left";
        }
      }
    }
  }
};
</script>

<style>
.mbatar-vue-slider {
  width: 100%;
  height: 100%;
  margin: 0 auto;
  position: relative;
  display: inline-block;
  overflow: hidden;
}
.mbatar-vue-slider .mbatar-vue-slider-slider-image {
  width: 100%;
  height: 100%;
}
.mbatar-vue-slider-controls {
  position: absolute;
  opacity: 0;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  transition: opacity 0.5s;
}

.mbatar-vue-slider:hover .mbatar-vue-slider-controls {
  transition: opacity 0.5s;
  opacity: 1;
}

.mbatar-vue-slider-controls .mbatar-vue-slider-control {
  cursor: pointer;
  width: 5rem;
  height: 5rem;
}
.mbatar-vue-slider-description {
  position: absolute;
  left: 0;
  bottom: 0;
  color: gray;
  padding: 10px;
  width: 100%;
  z-index: 1;
}
.mbatar-vue-slider-description a {
  text-decoration: none;
  color: gray;
}
.mbatar-vue-slider-description h3 {
  cursor: default;
}

/*---------------------------- FADE -------------------------*/

.mbatar-vue-slider-fade-enter-active,
.mbatar-vue-slider-fade-leave-active {
  transition: opacity 0.5s;
}
.mbatar-vue-slider-fade-enter,
.mbatar-vue-slider-fade-leave-active {
  opacity: 0;
}

/*-------------------------- SLIDE LEFT -------------------------*/

.mbatar-vue-slider-slide-left-enter-active {
  animation: slide-left-in 1s ease-out forwards;
}

.mbatar-vue-slider-slide-left-leave-active {
  animation: slide-left-out 1s ease-out forwards;
  position: absolute;
}

.mbatar-vue-slider-slide-left-move {
  transition: transform 1s;
}

@keyframes slide-left-in {
  from {
    transform: translateX(100%);
  }
  to {
    transform: translateX(0%);
  }
}

@keyframes slide-left-out {
  from {
    transform: translateX(0);
  }
  to {
    transform: translateX(-100%);
  }
}

/*--------------------------- SLIDE RIGHT ------------------------*/

.mbatar-vue-slider-slide-right-enter-active {
  animation: slide-right-in 1s ease-out forwards;
}
.mbatar-vue-slider-slide-right-leave {
}
.mbatar-vue-slider-slide-right-leave-active {
  animation: slide-right-out 1s ease-out forwards;
  position: absolute;
}
.mbatar-vue-slider-slide-right-move {
  transition: transform 1s;
}

@keyframes slide-right-in {
  from {
    transform: translateX(-100%);
  }
  to {
    transform: translateX(0%);
  }
}

@keyframes slide-right-out {
  from {
    transform: translateX(0%);
  }
  to {
    transform: translateX(100%);
  }
}
</style>



