<template>
  <div class="card" :class="visible ? 'is-flipped' : none" @click="selectCard">
    <div class="card-face is-front">
      <img :src="`/images/${value}.png`" :alt="value" class="icon"/>
      <img v-if="matched" src="/images/checkmark.svg" class="icon-checkmark" />
    </div>
    <div class="card-face is-back"></div>
  </div>
</template>

<script>
export default {
  props: {
    matched: {
      type: Boolean,
      default: false
    },
    position: {
      type: Number,
      required: true
    },
    value: {
      type: String,
      required: true
    },
    visible: {
      type: Boolean,
      default: false
    }
  },
  setup(props, context) {
    const selectCard = () => {
      context.emit('select-card', {
        position: props.position,
        faceValue: props.value
      })
    }
    return {
      selectCard
    }
  }
}
</script>


<style>
.card {
  position: relative;
  transition: 0.5s transform ease-in;
  transform-style: preserve-3d;
}

.card.is-flipped {
  transform: rotateY(180deg);
}

.card-face {
    width: 100%;
    height: 100%;
    position: absolute;
    border-radius: 10px;
    display: flex;
    align-items: center;
    justify-content: center;
    backface-visibility: hidden;
}

.card-face.is-front {
    background: linear-gradient(#90B2D8, #C1E3FF);
    color: white;
    box-shadow: 0px 2px 10px 0px #000;
    transform: rotateY(180deg);
}

.card-face.is-back {
    background-image: url("/images/card-globe-v3.jpg");
    background-position: center;
    background-repeat: no-repeat;
    background-size: cover;
    color: white;
    box-shadow: 0px 2px 10px 0px #000;
}

.icon-checkmark {
    position: absolute;
    right: 5px;
    bottom: 5px;
    max-width: 100%;
    max-height: 100%;
}

.card-icon {
    max-width: 100%;
    max-height: 100%;
}

img.icon {
    max-width: 100%;
    max-height: 100%;
}
</style>