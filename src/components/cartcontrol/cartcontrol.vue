<template>
  <div class="cartcontrol">
    <transition name="move">
        <div class="cart-decrease" v-show="food.count>0" @click = "decreaseCart">
          <span class="inner icon-remove_circle_outline"></span>
        </div>
    </transition>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click = "addCart"></div>
  </div>
</template>
<script type="text/ecmascript-6">
  import Vue from 'vue';
    export default{
        props:{
            food:{
                type:Object
            }
        },
        created(){
      },
        methods:{
//          添加商品
          addCart(event){
              if(!event._constructed){
//                  防止PC端多次触碰
                  return;
              }
              if(!this.food.count){
//                  Vue 的一个特性：当给一个观测对象添加一个不存在的属性的时候，是不可以的，
//                  html中的v-show是检测不到属性变化的  this.food.count = 1;
                    Vue.set(this.food,'count',1);
              }else{
                  this.food.count ++;
              }
//              开发cart.add事件 将点击事件目标作为参数传入 给 goods页面
            console.log(this.$emit(),888)
            this.$emit('cart.add',event.target);
          },
          decreaseCart(event){
          if(!event._constructed){
            return;
          }
          if(this.food.count){
              this.food.count--;
          }
        }
      }
    };
</script>
<style rel="stylesheet/scss" lang="scss">
  .cartcontrol{
    font-size: 0;
    .cart-decrease{
      display: inline-block;
      padding: 6px;
      transition: all 0.4s linear ;
      .inner {
        display: inline-block;
        font-size: 24px;
        line-height: 24px;
        color: rgb(0, 160, 220);
        transition: all .4s linear;
      }
      &.move-enter-active,&.move-leave-active{
        opacity: 1;
        transform: translate3D(0,0,0);
        .inner{
          transform: rotate(0);
        }
      }
      &.move-enter,&.move-leave-active{
        opacity: 0;
        transform: translate3d(24px,0,0);
        .inner{
          transform: rotate(180deg);
        }
      }
    }
    .cart-count{
      display: inline-block;
      vertical-align: top;
      width: 12px;
      padding-top: 6px;
      line-height: 24px;
      text-align: center;
      font-size: 10px;
      color: rgb(147,153,159);
    }
    .cart-add{
      display: inline-block;
      padding: 6px;
      font-size: 24px;
      line-height: 24px;
      color: rgb(0,160,220);
    }
  }
</style>
