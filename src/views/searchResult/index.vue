<template>
  <div class="search-result">
    <div class="search-title"><span>搜索</span> <p class="keywords text-of-single">{{getKw}}</p></div>
      <!--你可能感兴趣  -->
    <div class="may-interest" v-if="showMayIns">
      <p class="interest-title">你可能感兴趣</p>
      <div class="interest-list">
        <MayInterestItem v-for="(data,key) in mayIns" :key="key" :dataObj="data[0]" :type="key"/>
      </div>
    </div>
    <!-- 菜单列表 -->
    <el-menu
      mode="horizontal"
      router
      text-color="#373737"
    >
      <el-menu-item
        default-active="/searchResult"
        v-for="(item, index) in searchTypes"
        :index="'/searchResult' + item.path+ '?kw=' + getKw"
        :class="{'is-active':isActive(item)}"
        :key="item.path"
        active-text-color="#303133"
        >{{ item.title }}
      </el-menu-item>
    </el-menu>
      <router-view  class="result-list"/>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
export default {
  name: 'SearchResult',
  props: {
  },
  data () {
    return {
      searchTypes: [
        {
          title: "单曲",
          path: "/songs",
          type:1,
        },
        {
          title: "歌手",
          path: "/artists",
          type: 100,
        },
        {
          title: "歌单",
          path: "/playlists",
          type: 1000,
        },
        {
          title: "歌词",
          path: "/lyrics",
          type: 1006,
        },
        {
          title: "专辑",
          path: "/albums",
          type: 10,
        },
        {
          title: "视频",
          path: "/videos",
          type: 1014,
        },
        {
          title: "MV",
          path: "/mvs",
          type: 1004,
        },
        
        {
          title: "电台",
          path: "/djradios",
          type: 1009,
        },
        {
          title: "声音",
          path: "/sounds",
          type: 2000,
        },
        {
          title: "用户",
          path: "/userProfiles",
          type: 1002,
        },
      ],
      mayIns:{},//你可能感兴趣
    }
  },
  computed: { 
    ...mapGetters('search', ['getKw']),
    showMayIns(){
      return Object.keys(this.mayIns).length > 0
    },
  },
  watch: { 

  },
  components: { 
    MayInterestItem: () => import('./components/MayInterestItem.vue'),
  },
  methods: {
    isActive(menuItem){
      return this.$route.path === '/searchResult' + menuItem.path
    },
    //可能感兴趣
   async getMayInterest(){
      let { result  }= await this.$http(`/search/multimatch?keywords=${this.getKw}`) 
      delete result.orders
      this.mayIns = result
    },
  },
  created () { 
      this.getMayInterest()
  },
  mounted () { 
  },
}
</script>
<style scoped lang='scss'>
.search-result{
  color:#f00;
  padding:10px 24px;
  .search-title{
    color:#1d1d1f;
    font-size:18px;
    font-weight: 600;
    margin-top:4px;
    margin-bottom:10px;
    display: flex;
    align-items: center;
    .keywords{
      margin-left:8px;
      width:calc(100% - 44px);
    }
  }
  .may-interest{
    .interest-title{
      font-size:14px;
      color:#999;
      margin-bottom:10px;
    }
    .interest-list{
      display:flex;
      flex-wrap:wrap;
      ::v-deep .may-interest-item:nth-of-type(4n){
        margin-right:0;
      }
    }
  }
  // 搜索结果-菜单列表
  .el-menu {
    padding-bottom: 4px;
    margin-left: 20px;
    .el-menu-item {
      height: 50px;
      padding: 0;
      font-size: 14px;
      margin: 0 15px;
    }
   &.el-menu--horizontal {
      border: none !important;
    }
  }
  ::v-deep .el-menu--horizontal > .el-menu-item.is-active {
    font-size: 16px;
    font-weight: 600;
    border-bottom: 3px solid #ec4141 !important;
  }
  //结果
  .result-list{
    color:#666;
  }
}
</style>