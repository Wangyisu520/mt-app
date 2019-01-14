<template>
    <div class="star">
        <span class="star-item" v-for="(itemClass,index) in itemClass" :key="index" :class="itemClass"></span>
    </div>
</template>
 
<script>
const LENGTH = 5;
//星星状态类名
const CLS_ON = "on";
const CLS_HALF = "half";
const CLS_OFF = "off";
export default {
  props: {
    score: {
      type: Number
    }
  },
  computed: {
    itemClass() {
      let result = [];

      let score = Math.floor(this.score * 2) / 2;
        //半星
      let hasDecimal = score % 1 !== 0;
        //全星
      let integer = Math.floor(score);

      for (let i = 0; i < integer; i++) {
        result.push(CLS_ON);
      }
      if (hasDecimal) {
        result.push(CLS_HALF);
      }
      while (result.length < LENGTH) {
        result.push(CLS_OFF);
      }
      return result;
    }
  }
};
</script>

<style scoped>
.star {
  font-size: 0;
}
.star .star-item {
  display: inline-block;
  width: 10px;
  height: 10px;
  margin-right: 3px;
  background-repeat: no-repeat;
  background-size: 10px 10px;
}
.star .star-item:last-child {
  margin-right: 0;
}

.star .on {
  background-image: url(img/star24_on@2x.png);
}
.star .half {
  background-image: url(img/star24_half@2x.png);
}
.star .off {
  background-image: url(img/star24_off@2x.png);
}
</style>
