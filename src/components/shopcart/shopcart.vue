<template>
<div class="shopcart">
  <div class="content">
    <div class="content-left">
      <div class="logo-wrap">
        <div class="logo" :class="{'hightlight':totalCount>0}">
          <i class="icon-shopping_cart"></i>
        </div>
        <div class="num" v-show="totalCount>0">{{totalCount}}</div>
      </div>
      <div class="price" :class="{'hightlight':totalCount>0}">￥{{totalPrice}}</div>
      <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
    </div>
    <div class="content-right">
      <div class="pay" :class="payClass">
       {{payDesc}}
      </div>
    </div>
  </div>
  <!--抛物线小球  若干个-->
  <div class="ball-container">
    <!--<transition name="drop">-->
      <div v-for="ball in balls" v-show="ball.show" class="ball">
        <div class="inner"></div>
      </div>
    <!--</transition>-->
  </div>
</div>
</template>
<script type="text/ecmascript-6">
export default{
    props:{
        selectFoods:{
//            选择商品的数组
            type:Array,
            default(){
                return [];
            }
        },
        deliveryPrice:{
          type:Number,
          default:0
        },
        minPrice:{
        type:Number,
        default:0
        }
    },
    data() {
        return {
            balls:[
                {show:false},
                {show:false},
                {show:false},
                {show:false},
                {show:false}]
    };
    },
    computed:{
        totalPrice(){
//            计算选中商品的总价
          let total = 0;
          this.selectFoods.forEach((food)=>{
             total += food.price*food.count;
          });
          return total;
        },
        totalCount(){
//            选中商品数量的总和
          let count = 0;
          this.selectFoods.forEach((food)=>{
              count+=food.count;
          });
          return count;
        },
        payDesc(){
//          计算结算时的价格
          if(this.totalPrice === 0){
//              ES6语法 将内容放到制表符内，就不需要用+号连接；变量放在${}中
              return `￥${this.minPrice}元起送`;
          }else if(this.totalPrice < this.minPrice){
              let diff = this.minPrice - this.totalPrice;
              return `还差￥${diff}元起送`;
          }else{
              return '去结算';
          }
        },
        payClass(){
//            判断价格是否满足结算价格，来给结算按钮不同样式
          if(this.totalPrice < this.minPrice){
              return 'not-enough';
          }else{
              return 'enough';
          }
        }
  },
    methods:{
        drop(el){
            console.log(111);
        }
    }
};
</script>
<style rel="stylesheet/scss" lang="scss">
.shopcart{
  position: fixed;
  left: 0;
  bottom: 0;
  z-index: 50;
  width: 100%;
  height: 48px;
  .content{
    display: flex;
    font-size: 0;
    background:#141d27;
    margin-left: 0;
    color: rgba(255,255,255,.4);
    .content-left{
      flex:1;
      .logo-wrap{
        display: inline-block;
        position: relative;
        top: -10px;
        margin: 0 12px;
        padding: 6px;
        width: 56px;
        height: 56px;
        box-sizing: border-box;
        vertical-align: top;
        border-radius: 50%;
        background: #141d27;
        .logo{
          width: 100%;
          height: 100%;
          border-radius: 50%;
          background: #2b343c;
          text-align: center;
          i{
            font-size: 24px;
            color: #80858a;
            line-height: 44px;
          }
          &.hightlight{
            background: rgb(0,160,220);
            i{
              color: #fff;
            }
          }
          }
        .num{
          position: absolute;
          top: 0;
          right: 0;
          width: 24px;
          height: 16px;
          line-height: 16px;
          text-align: center;
          border-radius: 16px;
          font-size: 9px;
          font-weight: 700;
          color: #fff;
          background:rgb(240,20,20);
          box-shadow: 0 4px 8px 0 rgba(0,0,0,.4);
        }
      }
      .price{
        display: inline-block;
        vertical-align: top;
        line-height: 24px;
        margin-top: 12px;
        padding-right: 12px;
        box-sizing: border-box;
        border-right:1px solid rgba(255,255,255,.1);
        font-size: 16px;
        font-weight: 700;
        color: rgba(255,255,255,.4);
        &.hightlight{
          color: #fff;
        }
      }
      .desc{
        display: inline-block;
        vertical-align: top;
        line-height: 24px;
        margin: 12px 0 0 12px;
        font-size: 10px;
      }
    }
    .content-right{
      flex:0 0 150px;
      .pay{
        height: 48px;
        line-height: 48px;
        text-align: center;
        font-size: 12px;
        font-weight: 700;
        background: #2b333b;
        &.not-enough{
          background: #2b333b;
        }
        &.enough{
          background: #00b43c;
          color: #fff;
        }
      }
    }
  }
  .ball-container{
    .ball{
      position: fixed;
      left: 32px;
      bottom: 22px;
      z-index: 200;
      .inner{
        width: 16px;
        height: 16px;
        border-radius: 50%;
        background: rgb(0,16,220);
        transition: all .4s;
      }
      /*&.drop-enter-active,&.drop-leave-active{*/
          /*transition: all .4s;*/
      /*}*/
      /*&.drop-enter,&.drop-leave-active{*/
      /*}*/
    }
  }
}
</style>
