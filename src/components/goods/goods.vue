<template>
  <div class="goods">
    <!--左侧选项列表-->
    <div class="menu-wrap" ref="menu">
      <ul>
        <li v-for="(item,index) in goods" class="menu-item"  v-bind:class="{'current':currentIndex ===index}" @click="selectMenu(index,$event)">
          <span class="text border-1px">
            <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <!--右侧商品列表-->
    <div class="foods-wrap" ref="food">
      <ul>
        <li v-for="item in goods" class="food-list food-list-hook">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li v-for="food in item.foods" class="food-item">
              <div class="icon">
                <img :src="food.icon" alt="" width="57" height="57">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}份</span>
                  <span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span>
                  <span v-show="food.oldPrice" class="old">￥{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrap">
                  <!--添加商品数量组件  传入列表中的每一项-->
                  <cartcontrol :food ='food'></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <!--底部购物车-->
    <!--将数据传递给shopcart组件 selectFoods 存放着添加商品的数组-->
    <!--ref='shopcart 在父组件中使用自组件-->
    <shopcart ref='shopcart' :select-foods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from "better-scroll";
  import shopcart from "components/shopcart/shopcart";
  import cartcontrol  from 'components/cartcontrol/cartcontrol';
  const ERR_OK = 0;
  export default{
    props: {
      seller: {
        type: Object
      }
    },
    data(){
      return {
        goods: [],
        listHeight:[],
//        实时拿到右侧详情的Y值
        scrollY:0
      }
    },
    created(){
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
      this.$http.get('/api/goods').then((response) => {
        response = response.body;
        if (response.errno === ERR_OK) {
          this.goods = response.data;
//          在Vue中DOM发生变化，都是在nextTick函数回调之后，一般想操作原生DOM都是调用这个接口
          this.$nextTick(()=>{
            this._initScroll();
            this._calculateHeight();
          })
        }
      })
    },
    computed:{
//        定义左侧列表的索引
        currentIndex(){
          for(let i=0;i<this.listHeight.length;i++){
              let height1 = this.listHeight[i];
              let height2 = this.listHeight[i+1];
//              如果当前的停留,在当前和后一个之间
//            !height2 是防止+1后最后一个没有值;
              if(!height2 ||(this.scrollY >= height1 && this.scrollY < height2)){
                return i;
              }
          }
//          如果 listHeight 没有值的时候
          return 0;
        },
        selectFoods(){
//            遍历food
        let foods = [];
        this.goods.forEach((good)=>{
            good.foods.forEach((food)=>{
//                拿到 li中遍历的food  如果count 有值，说明它被选中
              if(food.count){
                  foods.push(food);
              }
            });
        });
        return foods;
        }
    },
    methods:{
      selectMenu(index,event){
//        传入点击列表项的index,及事件
//        BScroll中派发事件和普通点击事件有属性区别
//        原生浏览器没有_constructor    自定义事件的时候为true 那么我们去做派发
//         这么做是为了 统一  pc和手机端获取index
        if(!event._constructed){
          return;
        }
        let foodList = this.$refs.food.getElementsByClassName('food-list-hook');
        let el = foodList[index];
        this.foodsScroll.scrollToElement(el,300);
        console.log(index);
      },
      _initScroll(){
//            接受两个对象  1、dom  2、json
          this.meunScroll = new BScroll(this.$refs.menu,{click:true});
          this.foodsScroll = new BScroll(this.$refs.food,{
              click:true,
              probeType :3,
          });
          this.foodsScroll.on("scroll",(pos)=>{
//              检测scroll滚动的时候，实时返回滚动位置
              this.scrollY = Math.abs(Math.round(pos.y));

          })
        },
      _calculateHeight(){
          let foodList = this.$refs.food.getElementsByClassName("food-list-hook");
          let height =0;
          this.listHeight.push(height);
//          遍历获取每项食物分类的高度
          for(let i = 0;i<foodList.length;i++){
              let item = foodList[i];
              height+=item.clientHeight;
              this.listHeight.push(height);
          }
      },
      _drop(target){
//          调用子组件的方法
        this.ref.shopcart.drop(target);
      }
    },
    components:{
//      在模版中使用组件
        shopcart,
        cartcontrol
    },
    events:{
        'cart.add'(target){
//            处理购物车点击添加时的事件
          this._drop(target);
          console.log('ssss')
        }
    }
  };
</script>
<style rel="stylesheet/scss" lang="scss">
  @import "../../common/sass/mixin.scss";

  .goods {
    position: absolute;
    width: 100%;
    top: 174px;
    display: flex;
    overflow: hidden;
    bottom: 46px;
  }

  .menu-wrap {
    flex: 0 0 80px;
    width: 80px;
    background: #f3f5f7;
    .menu-item {
      display: table;
      height: 54px;
      width: 56px;
      line-height: 14px;
      padding: 0 12px;
      &.current{
        background: #fff;
        font-weight: 700;
        position: relative;
        margin-top: -1px;
        z-index: 10;
        .text{
          @include border-none();
        }
      }
      .icon {
        display: inline-block;
        width: 12px;
        height: 12px;
        margin-right: 2px;
        background-size: 12px;
        background-repeat: no-repeat;
        vertical-align: top;
        &.decrease {
          @include bg-image('decrease_3');
        }
        &.descount {
          @include bg-image('discount_3');
        }
        &.guarantee {
          @include bg-image('guarantee_3');
        }
        &.invoice {
          @include bg-image('invoice_3');
        }
        &.special {
          @include bg-image('special_3');

        }
      }
      .text {
        @include border-1px(rgba(7, 17, 27, .1));
        font-size: 12px;
        display: table-cell;
        width: 56px;
        vertical-align: middle;
      }
    }
  }

  .foods-wrap {
    flex: 1;
    .title {
      padding-left: 14px;
      height: 26px;
      line-height: 26px;
      border-left: 2px solid #d9dde1;
      font-size: 12px;
      color: rgb(147, 153, 159);
      background: #f3f5f7;
    }
    .food-item {
      display: flex;
      margin: 18px;
      padding-bottom: 18px;
      @include border-1px(rgba(7, 17, 27, .1));
      &:last-child {
        @include border-none();
        margin-bottom: 0;
      }
      .icon{
        flex: 0 0 57px;
        margin-right: 10px;
      }
      .content{
        flex: 1;
        .name{
          margin: 2px 0 8px;
          height: 14px;
          font-size: 14px;
          color:rgb(7,17,27);
          line-height: 14px;
        }
        .desc,.extra{
          margin-bottom: 8px;
          line-height: 10px;
          font-size: 10px;
          color: rgb(147,153,159);
        }
        .desc{
          line-height: 12px;
        }
        .extra{
          margin-bottom:0;
          &.count{
            margin-right: 12px;
          }
        }
        .price{
          font-weight: 700;
          line-height: 24px;
          .now{
            margin-right: 18px;
            font-size: 14px;
            color: rgb(240,20,20);
          }
          .old{
            text-decoration: line-through;
            font-size: 10px;
            color: rgb(147,153,159);
          }
        }
        .cartcontrol-wrap{
          position: absolute;
          right: 0;
          bottom: 12px;
        }
      }
    }
  }
</style>



