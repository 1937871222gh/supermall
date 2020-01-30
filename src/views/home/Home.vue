<template>
  <div id="home">
      <nav-bar  class="home-nav"><div slot="center">购物街</div></nav-bar>

      <tab-control  :titles="['流行','新款','精选']"
                  @tabClick="tabClick" ref="tabControl1" class="tab-control" v-show="isTabFixed"/>

      <scroll class="content" ref="scroll"
              :probe-type="3"
              :pull-up-load="true"
              @scroll="contentScroll"
              @pullingUp="loadMore">
        <home-swiper :banners="banners" @swiperImageLoad.once="swiperImageLoad"/>
        <recommend-view :recommends="recommends"/>
        <feature-view/>
        <tab-control  :titles="['流行','新款','精选']"
                      @tabClick="tabClick" ref="tabControl2"/>
        <goods-list :goods="showGoods"/>
      </scroll>

      <back-top @click.native="backClick" v-show="isShowBackTop"/>

  </div>
</template>

<script>
  import HomeSwiper from "./childComps/HomeSwiper";
  import RecommendView from "./childComps/RecommendView";
  import FeatureView from "./childComps/FeatureView";

  import NavBar from "components/common/navbar/NavBar";
  import TabControl from "components/content/tabControl/TabControl";
  import GoodsList from "components/content/goods/GoodsList";
  import Scroll from "components/common/scroll/Scroll";
  import BackTop from "components/content/backTop/BackTop";

  import {getHomeMultidata,getHomeGoods} from "network/home";
  import {debounce} from "common/utils";

  export default {
        name: "Home",
        data(){
            return{
                banners:[],
                recommends:[],
                goods: {
                    'pop':{page: 0, list: []},
                    'new':{page: 0, list: []},
                    'sell':{page: 0, list: []}
                },
                currentType:'pop',
                isShowBackTop:'false',
                tabOffsetTop: 0,
                isTabFixed: false
            }
        },
        computed: {
            showGoods(){
                return this.goods[this.currentType].list
            }
        },
        components: {
            // 轮播图
            HomeSwiper,
            // 列表单组件
            RecommendView,
            // 本周流行
            FeatureView,

            // 底部导航栏
            NavBar,
            // 组件选项卡
            TabControl,
            // 数据列表
            GoodsList,
            // BS滚动
            Scroll,
            // 上拉图标
            BackTop
        },
       created() {
            // 请求多个数据
            this.getHomeMultidata()

       //    请求商品数据
            this.getHomeGoods('pop')

            this.getHomeGoods('new')

            this.getHomeGoods('sell')

       },
       mounted(){
           const refresh = debounce( this.$refs.scroll.refresh,100)
           //    监听GoodsListItem中的图片加载完成
           this.$bus.$on('itemimageLoad',() =>{
             refresh()
           })

       },
      methods:{
            //事件监听相关方法
            //根据index取不同数据
            tabClick(index){
                switch (index) {
                    case 0:
                        this.currentType = 'pop'
                        break
                    case 1:
                        this.currentType = 'new'
                        break
                    case 2:
                        this.currentType = 'sell'
                        break
                }
                this.$refs.tabControl1.currentIndex = index;
                this.$refs.tabControl2.currentIndex = index;
            //    统一两个tabControl点击的index
            },

          backClick(){
                this.$refs.scroll.scrollTo(0,0,500)
          },
          contentScroll(position){
                this.isShowBackTop = -(position.y)>1000
              // 直接用表达式决定布尔值

              this.isTabFixed =  -(position.y)>this.tabOffsetTop
              // 决定是否吸顶
          },
          loadMore(){
              // console.log('上拉加载更多');
              // 加载记录的currentType
              this.getHomeGoods(this.currentType)
          },
          swiperImageLoad(){
              // 2获取tabControl的offsetTop
              this.tabOffsetTop=this.$refs.tabControl2.$el.offsetTop
          },
            //网络请求相关方法
            getHomeMultidata() {
                getHomeMultidata().then(res =>{
                    this.banners = res.data.banner.list;
                    this.recommends =res.data.recommend.list;
                })
            },

            getHomeGoods(type) {
                // 值得思考
                const page =this.goods[type].page + 1
                getHomeGoods(type,page).then(res => {
                    // console.log(res);
                    this.goods[type].list.push(...res.data.list)
                //    ES6语法将请求数据list push到data的对应goods里面
                    this.goods[type].page += 1
                    // 上拉完成调用这个方法才能再次上拉
                    this.$refs.scroll.finishPullUp()
                })
            }
      }
  }
</script>

<style scoped>
   #home{

     height: 100vh;
     position: relative;
   }
  .home-nav{
    background-color: var(--color-tint);
    font-weight: 700;
    color: #ffffff;
    /*position: fixed !important;*/
    /*top: 0;*/
    /*left: 0;*/
    /*right: 0;*/
  }
  .content{
    position: absolute;
    top: 44px;
    bottom: 49px;
    right: 0;
    left: 0;
    overflow: hidden;
  }
  .tab-control{
    background-color: white;
    position: relative;
    z-index: 9;
  }
</style>
