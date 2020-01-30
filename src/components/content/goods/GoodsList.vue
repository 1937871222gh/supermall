<template>
  <div class="goods">
    <goods-list-item v-for="item in goods" :goods-item="item"/>
<!--    通过props获取home的goods数据，再遍历出来，通过父子组件传值方式再把数据传给GoodsListItem-->
  </div>
</template>

<script>
  import GoodsListItem from "./GoodsListItem";
    export default {
        name: "GoodsList",
        components: {
            GoodsListItem
        },
        props:{
            goods: {
                type:Array,
                default(){
                    return []
                }
            }
        }
    }
</script>

<style scoped>
  .goods{
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
  }
</style>
