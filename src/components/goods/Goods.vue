<template>
    <div class="goods">
        <!-- 左侧 -->
        <div class="menu-wrapper" ref="menuScroll">
            <ul>
                <li @click="selectMenu(0)" class="menu-item" :class="{'current': currentIndex === 0}">
                    <p class="text">
                        <img class="icon" :src="container.tag_icon" v-if="container.tag_icon" alt="">
                        {{container.tag_name}}
                    </p>
                </li>
                <li @click="selectMenu(index + 1)" :class="{'current': currentIndex === index+1}" class="menu-item" v-for="(item,index) in goods" :key="index">
                    <p class="text">
                        <img class="icon" :src="item.icon" v-if="item.icon" alt="">
                        {{item.name}}
                    </p>
                    <i class="num" v-show="calculateCount(item.spus)">{{calculateCount(item.spus)}}</i>
                </li>
            </ul>
        </div>

        <!-- 右侧 -->
        <div class="foods-wrapper" ref="foodScroll">
            <ul>
                <!-- 专场 -->
                <li class="foods-wrapper">
                    <ul>
                        <li class="container-list food-list-hook">
                            <div v-for="(item,index) in container.operation_source_list" :key="index">
                                <img :src="item.pic_url" alt="">
                            </div>
                        </li>
                    </ul>
                </li>
                <!-- 详细分类 -->
                <li v-for="(item,index) in goods" :key="index" class="food-list food-list-hook">
                    <h3 class="title">{{item.name}}</h3>
                    <!-- 单个商品列表 -->
                    <ul>
                        <li class="food-item" v-for="(food,index) in item.spus" :key="index" @click="showDetail(food)">
                            <div class="icon" :style="head_bg(food.picture)"></div>
                            <div class="content">
                                <h3 class="name">{{food.name}}</h3>
                                <p v-if="food.description" class="desc">{{food.description}}</p>
                                 <div class="extra">
                                    <span class="saled">{{food.month_saled_content}}</span>
                                    <span class="praise">{{food.praise_content}}</span>
                                </div>
                                <img class="product"  :src="food.product_label_picture" alt="">
                                <p class="price">
                                    <span class="text">￥{{food.min_price}}</span>
                                    <span class="unit">/{{food.unit}}</span>
                                </p>
                            </div> 
                            <div class="cartcontrol-wrapper">
                              <app-cart-control :food="food"></app-cart-control>
                            </div>
                        </li>
                    </ul>
                </li>
            </ul>
        </div>
      <!-- 购物车 -->
        <app-shopcart :poiInfo="poiInfo" :selectFoods="selectFoods"></app-shopcart>

        <!-- 商品详情 -->
        <app-product-deetail :food="selectFood" ref="foodView"></app-product-deetail>
    </div>
</template>

<script>
import BScroll from "better-scroll";
import ShopCart from "../shopcart/ShopCart";
import CartControl from '../cartcontrol/CartControl'
import ProductDetail from '../productDetail/ProductDetail'
export default {
  data() {
    return {
      container: {},
      goods: [],
      listHeight: [],
      menuScroll: {},
      foodScroll: {},
      scrollY: 0,
      poiInfo:{},
      selectFood:{}
    };
  },
  methods: {
    head_bg(imgName) {
      return "background-image:url(" + imgName + ")";
    },
    initScroll() {
      this.menuScroll = new BScroll(this.$refs.menuScroll,{
        click:true
      });
      this.foodScroll = new BScroll(this.$refs.foodScroll, {
        probeType: 3,
        click: true
      });
      this.foodScroll.on("scroll", pos => {
        // console.log(pos.y)
        this.scrollY = Math.abs(Math.round(pos.y));
      });
    },
    calculateHeight() {
      let foodlist = this.$refs.foodScroll.getElementsByClassName(
        "food-list-hook"
      );
      // console.log(foodlist)
      let height = 0;
      this.listHeight.push(height);

      for (let i = 0; i < foodlist.length; i++) {
        let item = foodlist[i];
        height += item.clientHeight;
        this.listHeight.push(height);
      }
      // console.log(this.listHeight)
    },
    selectMenu(index) {
      let foodlist = this.$refs.foodScroll.getElementsByClassName("food-list-hook");
      let element = foodlist[index];
      this.foodScroll.scrollToElement(element, 250);
    },
    calculateCount(spus){
      let count = 0;
      spus.forEach(food =>{
        if(food.count> 0){
          count += food.count
        }
      })
      return count
    },
    showDetail(food){
      this.selectFood = food
      this.$refs.foodView.showView();
    }
  },
  created() {
    // fetch("/api/goods")
    //   .then(res => {
    //     return res.json();
    //   })
    //   .then(response => {
    //     if (response.code == 0) {
    //       this.container = response.data.container_operation_source;
    //       this.goods = response.data.food_spu_tags;
    //       this.poiInfo = response.data.poi_info;
    //       // console.log(this.poiInfo)
    //       //DOM更新后执行
    //       this.$nextTick(() => {
    //         //执行滚动
    //         this.initScroll();

    //         this.calculateHeight();
    //       });
    //     }
    //   });

    fetch("https://wd2151358536yxwfcu.wilddogio.com/data.json")
      .then(res => {
        return res.json();
      })
      .then(response => {
        
          this.container = response.container_operation_source;
          this.goods = response.food_spu_tags;
          this.poiInfo = response.poi_info;
          // console.log(this.poiInfo)
          //DOM更新后执行
          this.$nextTick(() => {
            //执行滚动
            this.initScroll();

            this.calculateHeight();
          });
        
      });

      
  },
  computed: {
    currentIndex() {
      for (let i = 0; i < this.listHeight.length; i++) {
        let height1 = this.listHeight[i];
        let height2 = this.listHeight[i + 1];

        if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
          return i;
        }
      }
      return 0;
    },
    selectFoods(){
      let foods=[]
      this.goods.forEach(myfood =>{
        myfood.spus.forEach( food =>{
          if(food.count > 0){
            foods.push(food)
          }
        })
      })
      return foods;
    }
  },
  components: {
    "app-shopcart": ShopCart,
    "app-cart-control": CartControl,
    "app-product-deetail" : ProductDetail
  }
};
</script>

<style scoped>
.goods {
  display: flex;
  position: absolute;
  top: 190px;
  bottom: 51px;
  overflow: hidden;
  width: 100%;
}
.goods .menu-wrapper {
  flex: 0 0 85px;
  background: #f4f4f4;
}
.goods .foods-wrapper {
  flex: 1;
  /* background: blue; */
}
/* 左侧 */
.goods .menu-wrapper .menu-item {
  padding: 16px 23px 15px 10px;
  border-bottom: 1px solid #e4e4e4;
  position: relative;
}

.goods .menu-wrapper .menu-item .text {
  font-size: 13px;
  color: #333333;
  line-height: 17px;
  vertical-align: middle;
  -webkit-line-clamp: 2;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.goods .menu-wrapper .menu-item .text .icon {
  width: 15px;
  height: 15px;
  vertical-align: middle;
}
/* 专场样式 */
.goods .foods-wrapper .container-list {
  padding: 11px 11px 0 11px;
  border-bottom: 1px solid #e4e4e4;
}

.goods .foods-wrapper .container-list img {
  width: 100%;
  margin-bottom: 11px;
  border-radius: 5px;
}
/* 详情分类 */
.goods .foods-wrapper .food-list {
  padding: 11px;
}

.goods .foods-wrapper .food-list .title {
  height: 13px;
  font-size: 13px;
  background: url(./img/btn_yellow_highlighted@2x.png) no-repeat left center;
  background-size: 2px 10px;
  padding-left: 7px;
  margin-bottom: 12px;
}

.goods .foods-wrapper .food-list .food-item {
  display: flex;
  margin-bottom: 25px;
  position: relative;
}

.goods .foods-wrapper .food-list .food-item .icon {
  flex: 0 0 63px;
  background-position: center;
  background-size: 120% 100%;
  background-repeat: no-repeat;
  margin-right: 11px;
  height: 75px;
}
.goods .foods-wrapper .food-list .food-item .content {
  flex: 1;
}

.goods .foods-wrapper .food-list .food-item .content .name {
  font-size: 16px;
  line-height: 21px;
  color: #333333;
  margin-bottom: 10px;
  padding-right: 27px;
}

.goods .foods-wrapper .food-list .food-item .content .desc {
  font-size: 10px;
  line-height: 19px;
  color: #bfbfbf;
  margin-bottom: 8px;
  -webkit-line-clamp: 1;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.goods .foods-wrapper .food-list .food-item .content .extra {
  font-size: 10px;
  color: #bfbfbf;
  margin-bottom: 7px;
}
.goods .foods-wrapper .food-list .food-item .content .extra .saled {
  margin-right: 14px;
}
.goods .foods-wrapper .food-list .food-item .content .product {
  height: 15px;
  margin-bottom: 6px;
}
.goods .foods-wrapper .food-list .food-item .content .price {
  font-size: 0;
}
.goods .foods-wrapper .food-list .food-item .content .price .text {
  font-size: 14px;
  color: #fb4e44;
}
.goods .foods-wrapper .food-list .food-item .content .price .unit {
  font-size: 12px;
  color: #bfbfbf;
}

/* 选中样式 */
.goods .menu-wrapper .menu-item.current {
  background: white;
  font-weight: bold;
  margin-top: -1px;
}
.goods .menu-wrapper .menu-item:first-child.current {
  margin-top: 1px;
}

.goods .foods-wrapper .food-list .food-item .cartcontrol-wrapper {
  position: absolute;
  right: 0;
  bottom: 0;
}

.goods .menu-wrapper .menu-item .num {
  position: absolute;
  right: 5px;
  top: 5px;
  width: 13px;
  height: 13px;
  border-radius: 50%;
  color: white;
  background: red;
  text-align: center;
  font-size: 7px;
  line-height: 13px;
}
</style>
