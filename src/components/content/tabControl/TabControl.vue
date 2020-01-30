<template>
    <div class="tab-control">
      <div v-for="(item,index) in titles"
           class="tab-control-item"
           :class="{active:index ===currentIndex}" @click="itemClick(index)">
        <!--当前的点击的时候会把index传递到currentIndex，如果两个相等则为true，对应的class样式属性为true，也就可以显示出样式-->
        <span>{{item}}</span>
      </div>
    </div>
</template>

<script>
    export default {
        name: "TabControl",
        data(){
            return{
                currentIndex:0
            }
        },
        methods:{
            itemClick(index){
                this.currentIndex = index;
                this.$emit('tabClick',index)
            //    自定义事件将点击的选项传给父组件
            }
        },
        props: {
            titles: {
                type:Array,
                default(){
                    return []
                }
            }
        }
    }
</script>

<style scoped>
  .tab-control{
    display: flex;
    text-align: center;
    font-size: 15px;
    height: 40px;
    line-height: 40px;
   }
  .tab-control-item{
    flex: 1;
  }
  .tab-control-item span{
    padding: 5px;
  }
  .active span{
    color: var(--color-high-text);
    border-bottom: 3px solid var(--color-tint);
  }
</style>
