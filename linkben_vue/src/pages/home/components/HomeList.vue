<template>
  <div class="wrapper">
    <ul v-infinite-scroll="loadMore" infinite-scroll-disabled="isMoreLoading" infinite-scroll-distance="10"
      :infinite-scroll-immediate-check="true" class="newsList">
      <li v-for="(item,index) in newsList" class="flex">
        <h3 class="num">{{item.indexnum}}</h3>
        <router-link :to="'item'+'?id='+item.id" target="_blank" class="flex-item">
          <h4 class=" title">{{item.title}}</h4>
        </router-link>
        <div class="img-r item-img">
          <img :src="item.images[0]">
        </div>
      </li>
    </ul>
    <div class="loading-box tc center " v-if="isLoading">
      <mt-spinner type="snake" class="loading-more"></mt-spinner>
      <span class="loading-more-txt">加载中...</span>
    </div>
    <div class="no-more" v-if="noMore">没有更多了~</div>
  </div>
</template>

<script>
  import axios from 'axios'
  export default {
    name: 'homeList',
    components: {},
    data() {
      return {
        title: 'home的列表内容',
        newsList: [],
        moreList: [],
        dataNum: 1,
        isMoreLoading: false, // 加载更多中
        isLoading: false,
        noMore: false,
        contentDate:0,//知乎返回来的日期
        list_index:1,
      }
    },
    props: {},
    mounted() {},
    computed: {

    },
    created() {
      var that = this;
      let i = 0;
      debugger;
      var date = new Date((new Date()).getTime()+ 24*60*60*1000) //时间戳为10位需*1000，时间戳为13位的话不需乘1000
      var Y = date.getFullYear();
      var M = (date.getMonth() + 1 < 10 ? '0' + (date.getMonth() + 1) : date.getMonth() + 1);
      var D = (date.getDate() + 1 < 10 ? '0' + (date.getDate() + 1) : date.getDate() + 1);
      var url = "/zhihu/api/4/news/before/" + Y + M + D;
      axios.get(url).then(function (res) {
        that.contentDate=res.data.date;
        var list_content = [];
        $(res.data.stories).each(function (index, element) {
          var urlImage = (element.images[0]).replace("https://", "http://");
          var index=that.list_index;
          element.indexnum=index;
          that.list_index++;
          element.images[0] = urlImage;
          list_content.push(element);
        });
        that.newsList = list_content;

      }).catch(function (error) {
        console.info("data3", error)
      });
    },
    methods: {
      //滚动条的内容的使用
      loadMore() {
        this.isMoreLoading = true;
        this.isLoading = true;
        this.noMore = false;
        setTimeout(() => {
          var that = this;
          var url = "/zhihu/api/4/news/before/" +that.contentDate;
          axios.get(url).then(function (res) {
           that.contentDate=res.data.date;
            that.moreList = res.data.stories;
            if (that.moreList.length == 0) {
              that.noMore = true;
              that.isLoading = false;
            } else {
              that.isLoading = false;
              that.moreList.forEach(function (item) {
                var urlImage = (item.images[0]).replace("https://", "http://");
                var index=that.list_index;
                item.indexnum=index;
                that.list_index++;
                item.images[0] = urlImage;
                that.newsList.push(item);
              })
            }
          }).catch(function (error) {







            
            console.info(error)
          });
          this.noMore = false;
          this.isMoreLoading = false;
        }, 1000);
      }
    },

  }
</script>

<style scoped>
  .newsList {
  
  }
  .newsList  li{
    border-bottom: 0.2rem solid #eee;
    padding-bottom: 1.5rem;
    margin-bottom: 1.5rem;
    border-radius: 0.4rem;
    cursor: pointer;
    height: auto;
  }
  .newsList  li .title{
    color: #474D58;
    margin: 0rem 1.5rem;
    padding-left: 0rem;
    line-height: 2.5rem;
    font-weight: 600;
  }
  .num{
    color: #FF9607;
    font-weight: 600;
  
    text-align: center;
  }
  .item-img{
    width: 9rem;
    height: 9rem;
    margin-left: 1.5rem;
  }
</style>