<template>
    <div>
        <!-- {{msg}} -->
        <!-- 搜索 -->
        <div class="search-bar">
            <van-row>
                <van-col span="3">
                    <img :src="locationIcon" alt="" width="80%">
                </van-col>
                <van-col span="16">
                    <input type="text" class="search-input"/>
                </van-col>
                <van-col span="5">
                    <van-button size="mini">查找</van-button>
                </van-col>
            </van-row>
        </div>
        <!-- 轮播 -->
        <div class="swiper-area">
            <van-swipe :autoplay="3000" indicator-color="white">
                <van-swipe-item v-for="(banner,index) in bannerPicArray" :key="index">
                    <img v-lazy="banner.image" width="100%" height="100%">
                </van-swipe-item>
            </van-swipe>
        </div>
        <!-- 第一块导航 -->
        <div class="type-bar">
            <div v-for="(item,index) in category" :key="index">
                <img v-lazy="item.image" width="90%" />
                <span>{{item.mallCategoryName}}</span> 
            </div>
        </div>
        <!-- ad-banner -->
        <div class="ad-banner">
            <img v-lazy="adBanner.PICTURE_ADDRESS" width="100%">
        </div>
        <!-- 第一块内容end -->
        <div class="recommend-area">
            <div class="recommend-title">内容推荐</div>
            <div class="recommon-body">
                <swiper :options="swiperOption" ref="">
                    <swiper-slide v-for="(item,index) in recommendGoods" :key="index">
                        <div class="recommend-item">
                            <img :src="item.image" alt="" width="80%">
                            <div>{{item.goodsName}}</div>
                            <div>&yen;{{item.price | moneyFilter}}(&yen;{{item.mallPrice | moneyFilter}})</div>
                            
                        </div>
                    </swiper-slide>
                </swiper>
            </div>
        </div>
        <swiperDefault></swiperDefault>
        <!-- 不规则布局 -->
        <!-- 楼层分布 -->
        <!-- <div class="floor">
            <div class="floor-anomaly">
                <div class="floor-one"><img :src="floor1_0.image" width="100%" /></div>
                <div>
                    <div class="floor-two"><img :src="floor1_2.image" width="100%" /></div>
                    <div><img :src="floor1_2.image" width="100%" /></div>
                </div>
            </div> -->
            <!-- 规则布局 -->
            <!-- <div class="floor-rule">
                <div v-for="(item,index) in floor1.slice(3)" :key="index">
                    <img :src="item.image" alt="" width="100%">
                </div>
            </div>
        </div> -->
       <floorCompoent :floorData="floor1" :floorTitle="floorName.floor1"></floorCompoent>
       <floorCompoent :floorData="floor2" :floorTitle="floorName.floor2"></floorCompoent>
       <floorCompoent :floorData="floor3" :floorTitle="floorName.floor3"></floorCompoent>
       <!-- 首页热卖商品 -->
       <!-- Van-list组件 -->
       <div class="hot-area">
           <div class="hot-title">热卖商品</div>
           <div class="hot-goods">
                <van-list>
                    <van-row gutter="20">
                        <van-col span="12" v-for="( item, index) in hotGoods" :key="index">
                            <div>{{item.name}}</div>
                        </van-col>
                    </van-row>
                </van-list>
           </div>
       </div>
    </div>
</template>

<script>
    import axios from 'axios'
    //以组件形式引入
    import {swiper,swiperSlide} from 'vue-awesome-swiper'
    import 'swiper/dist/css/swiper.css'
    // 引入轮播组件
    import swiperDefault from './swiperDefault.vue'
    // 引入内容组件
    import floorCompoent from './FloorComponent.vue'
    //引入过滤
    import {toMoney} from '@/filter/moneyFilter.js'
    // 引入数据地址
    import url from '@/serviceAPI.config.js'
    export default {
        data(){
            return{
                msg:'shopping mall',
                locationIcon:require('../assets/images/location.png'),
                // bannerPicArray:[
                //    {imageUrl:'http://m.eyehx.com/uploads/181226/10-1Q2261633511X.jpg'}, {imageUrl:'http://m.eyehx.com//uploads/171102/181229/181229/1-1Q22Z93133537.jpg'},{imageUrl:'http://m.eyehx.com//uploads/171102/181229/181229/1-1Q22Z93133537.jpg'}
                // ],
                bannerPicArray:[],
                category:[],
                adBanner:'',
                // 推荐内容
                recommendGoods:[],//推荐内容
                // 中间推荐内容
                swiperOption:{
                    // 放swiper参数
                    pagination:{

                    },
                    // 可见参数
                    slidesPerView:3
                },
                // 楼层区域布局start
                floor1:[],
                floor1_0:{},
                floor1_1:{},
                floor1_2:{},
                floor1_3:{},
                floor2:[],
                floor3:[],
                //标题
                floorName:{},
                hotGoods:[],
                hotGoods:[],//热卖商品
            }
        },
        // 属性
        // 过滤
        filters:{
            moneyFilter(money){
                return toMoney(money)
            }
        },
        // 注册组件
        components:{
            swiper,
            swiperSlide,
            swiperSlide,
            swiperDefault,
            floorCompoent
        },
        created(){
            axios({
                url:url.getShoppingMallInfo,
                method:'get',
            }).then(response => {
                if(response.status == 200){
                    this.category=response.data.data.category;
                    // 广告图
                    this.adBanner = response.data.data.advertesPicture;
                    // 轮播图
                    this.bannerPicArray = response.data.data.slides
                    this.recommendGoods = response.data.data.recommend
                     // 楼层区域布局1数据
                    this.floor1 = response.data.data.floor1 //楼层1数据
                    this.floor1_0 =this.floor1[0]
                    this.floor1_1 =this.floor1[1]
                    this.floor1_2 =this.floor1[2]
                    this.floor2 = response.data.data.floor2 //楼层1数据
                    this.floor3 = response.data.data.floor3 //楼层1数据
                    // 标题
                    this.floorName = response.data.data.floorName
                    // 热搜内容
                    this.hotGoods = response.data.data.hotGoods
                }
            });
        }
    }
</script>

<style scoped>
    .search-bar{
        height: 2.2rem;
        background-color: #e5017d;
        line-height: 2.2rem;
        overflow: hidden;
    }
    .search-input{
        width:100%;
        height:1.3rem;
        border:0;
        border-bottom:1px solid #fff!important;
        background-color: #e5017d;
        color:#fff;
    }
    /* 轮播效果 */
    .swiper-area{
        max-height:12rem;
        width:100%;
        clear: both;
        overflow: hidden;
    }
    .type-bar{
        background: #fff;
        margin:0 0.3rem 0.3rem 0.3rem;
        border-radius: 0.3rem;
        font-size:14px;
        display: flex;
        flex-direction: row;
        flex-wrap: nowrap;
    }
    .type-bar div{
        padding:.3rem;
        font-size:12px;
        text-align: center;
        flex:1;
    }
    .recommend-area{
       background-color: #fff;
       margin-top: .3rem;
    }
    .recommend-title{
        border-bottom:1px solid #eee;
        font-size:14px;
        padding:.2rem;
        color:#e5017d;
    }
    .recommend-body{
        border-bottom:1px solid #eee;
    }
    .recommend-item{
        width:99%;
        border-right:1px solid #eee;
        font-size:12px;
        text-align: center;
    }
    .hot-area{
        text-align: center;
        font-size:14px;
        height: 1.8rem;
        line-height:1.8rem;
    }
    .hot-goods{
        height: 130rem;
        overflow: hidden;
        background-color: #fff;
    }
     /* 楼层样式 */
    .floor-anomaly{
      display: flex;
      flex-direction:row;
      background-color: #fff;
      border-bottom:1px solid #ddd;
    }
    .floor-anomaly div{
        width:10rem;
        box-sizing: border-box;
        -webkit-box-sizing: border-box;
    }
    .floor-one{
        border-right:1px solid #ddd;
    }
    .floor-two{
        border-bottom:1px solid #ddd;
    }

    /* 剩下内容样式start */
    .floor-rule{
        display: flex;
        flex-direction: row;
        flex-wrap: wrap;  
        background-color:#fff;
    }
    .floor-rule  div {
        -webkit-box-sizing:border-box;
        box-sizing:border-box; 
        width: 10rem;
        border-bottom:1px solid #ddd;
    }
    .floor-rule div:nth-child(odd){
        border-right: 1px solid #ddd;
    }
    /* 剩下的内容start */
    .floor-rule{
        display: flex;
        flex-direction: row;
        flex-wrap:wrap;
        background: #fff;
    }
    .floor-rule div{
        -webkit-box-sizing: border-box;
        box-sizing: border-box;
        width: 10rem;
        border-bottom: 1px solid #ddd;
    }
    .floor-rule div:nth-child(odd){
        border-right: 1px solid #ddd;
    }
    /* 剩下的内容end */
    /* 热卖商品 */
    .hot-area{
      text-align: center;
      font-size:14px;
      height: 1.8rem;
      line-height:1.8rem;
    }
</style>