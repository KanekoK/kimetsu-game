<template>
  <div class="wrapper">
    <section class="header">
      <div class="point">
        <span>ポイント：</span><span ref="score" class="score">{{ points }}</span>
      </div>
    </section>

    <section class="container">
      <h1 class="title"><img src="~/assets/title.png" alt="鬼滅の刃"></h1>

      <section class="main">

        <div class="contents">

          <div class="left">
            <img class="tanjirou" :src="tanjirouImg">
            <img ref="bakuhatsu" class="bakuhatsu" src="~/assets/bakuhatsu.png">
          </div>

          <div ref="obstacle" class="obstacle">
            <img ref="maruta" class="maruta" src="~/assets/maruta.png">
          </div>

          <div class="right">
            <img class="inosuke" src="~/assets/04.png">
            <img class="nezuko" src="~/assets/03.png">
          </div>

        </div>
      </section>

    </section>

  </div>
</template>

<script>

const hitRange = 100;

/**
 * 丸太がたんじろうにヒットしたかどうかの判定
 */
function collisionDetection(maruta) {
  let marutaLocation = maruta.getBoundingClientRect().left;

  console.log('Maruta Location: ', marutaLocation);

  if (-200 < marutaLocation && marutaLocation < hitRange) {
    return true;
  } else {
    return false;
  }
}

/**
 * スコア
 */
function score(points, detection) {
  if (detection) {

    points = points + 100;
  } else {
    points = points - 30;
  }
  return points
}

/**
 * エフェクトの制御
 */
function effecter(detection, refs) {
  if (detection) {
    refs.maruta.style.display = 'none';
    refs.bakuhatsu.style.display = 'block';
    refs.score.style.color = 'red';

    setTimeout(() => {
      // デフォルトに戻す
      refs.maruta.style.display = 'block';
      refs.bakuhatsu.style.display = 'none';
      refs.score.style.color = 'white';

      changeSpeed(refs);
    }, 600)

  } else {
    refs.score.style.color = 'blue';
    setTimeout(() => {
      refs.score.style.color = 'white';
      changeSpeed(refs);
    }, 600)
  }
}

function changeSpeed(refs) {
  let maruta = refs.maruta.getBoundingClientRect().left;
  console.log(maruta)

  if (maruta < -200) {
    let speedsClass = ['obstacle-high', 'obstacle-slow', 'obstacle'];
    refs.obstacle.className = speedsClass[Math.floor(Math.random() * speedsClass.length)];
  }
}

export default {

  data() {
    return {
      tanjirouImg: require('~/assets/02.png'),
      points: 0
    }
  },

  created() {
    if (process.client) {
      window.addEventListener('keydown', this.keyAction);
    }
  },

  methods: {
    keyAction(e) {
      if (e.keyCode == 13) {
        this.tanjirouImg = require('~/assets/05.png');
        setTimeout(() => {
          this.tanjirouImg = require('~/assets/02.png');

          let detection = collisionDetection(this.$refs.maruta);

          effecter(detection, this.$refs);

          this.points = score(this.points, detection);

        }, 500);
      }

    },
  }
}
</script>

<style lang="scss" scoped>

.header {
  .point {

    color: #ffffff;
    font-size: 36px;
    font-weight: bold;
    position: fixed;
    top: 60px;
    left: 60px;
  }
}

.title {
  font-family:
    'Quicksand',
    'Source Sans Pro',
    -apple-system,
    BlinkMacSystemFont,
    'Segoe UI',
    Roboto,
    'Helvetica Neue',
    Arial,
    sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
  text-align: center;

  img {
    width: 750px;
  }
}

.main {
  width: 100%;
  height: 100%;
  position: absolute;
  bottom: 0;

  background-image: url('~assets/background.jpg');
  background-size: cover;
  background-repeat: no-repeat;

  z-index: -999;
}

.contents {
  display: flex;
  justify-content: space-between;

  .obstacle {
    position: fixed;
    width: 100%;
    bottom: 100px;
    animation: flowing 4s linear infinite;
    transform: translateX(65%);/*初期位置*/
  }
  .obstacle-high {
    position: fixed;
    width: 100%;
    bottom: 100px;
    animation: flowing 2s linear infinite;
    transform: translateX(65%);/*初期位置*/
  }
  .obstacle-slow {
    position: fixed;
    width: 100%;
    bottom: 100px;
    animation: flowing 6s linear infinite;
    transform: translateX(65%);/*初期位置*/
  }

  @keyframes flowing {
    100% {
      transform: translateX(-100%);
    }
  }
  .right {
    position: fixed;
    right: 0;
    bottom: 0;
  }

  .tanjirou {
    width: 250px;
    position: fixed;
    bottom: 0;
  }
  .inosuke {
    width: 270px;
  }
  .nezuko {
    width: 300px;
  }

  .maruta {
    width: 200px;
  }
  .bakuhatsu {
    width: 200px;
    position: fixed;
    left: 100px;
    bottom: 60px;
    display: none;
  }
}


</style>
