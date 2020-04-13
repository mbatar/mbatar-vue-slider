<template>
  <div id="slider" @mouseenter="onMouseEnter" @mouseleave="onMouseLeave">
    <transition-group
      :name="currentSlideMode"
      mode="out-in"
      @after-enter="enter"
      @before-leave="before"
    >
      <img
        id="image"
        :src="options.slides[currentSlide].src"
        :key="options.slides[currentSlide].id"
        class="slider-image"
      />
      <div
        class="description"
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
    <div id="controls">
      <img class="control" src="./assets/previous.png" @click="prevSlide" />
      <img class="control" src="./assets/next.png" @click="nextSlide" />
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
      if (this.currentSlideMode === "fade") {
        this.currentSlide++;
      } else {
        this.currentSlideMode = "slide-left";
        this.currentSlide++;
      }
    },
    prevSlide() {
      if (this.currentSlideMode === "fade") {
        this.currentSlide--;
      } else {
        this.currentSlideMode = "slide-right";
        this.currentSlide--;
      }
    },
    onMouseEnter() {
      this.showControl = true;
    },
    onMouseLeave() {
      this.showControl = false;
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
        this.currentSlideMode = "slide-left";
      } else {
        this.currentSlideMode = this.options.mode;
      }
    } else {
      this.currentSlideMode = "slide-left";
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
        if (this.currentSlideMode === "slide-right") {
          this.currentSlideMode = "slide-left";
        }
      }
    }
  }
};
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  overflow: hidden;
}
#slider {
  width: 100%;
  height: 100%;
  margin: 0 auto;
  position: relative;
  display: inline-block;
}
#slider .slider-image {
  width: 100%;
  height: 100%;
}
#controls {
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

#slider:hover #controls {
  transition: opacity 0.5s;
  opacity: 1;
}

#controls .control {
  cursor: pointer;
  width: 5rem;
  height: 5rem;
}
.description {
  position: absolute;
  left: 0;
  bottom: 0;
  color: gray;
  padding: 10px;
  width: 100%;
}
.description a {
  text-decoration: none;
  color: gray;
}

/*---------------------------- FADE -------------------------*/

fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter,
.fade-leave-active {
  opacity: 0;
}

/*-------------------------- SLIDE LEFT -------------------------*/

.slide-left-enter-active {
  animation: slide-left-in 1s ease-out forwards;
}

.slide-left-leave-active {
  animation: slide-left-out 1s ease-out forwards;
  position: absolute;
}

.slide-left-move {
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

.slide-right-enter-active {
  animation: slide-right-in 1s ease-out forwards;
}
.slide-right-leave {
}
.slide-right-leave-active {
  animation: slide-right-out 1s ease-out forwards;
  position: absolute;
}
.slide-right-move {
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

