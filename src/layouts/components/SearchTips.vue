<!--
 * @Author: FXJ
 * @LastEditTime: 2022-10-16 23:20:58
 * @FilePath: \vue-wyy-music\src\components\layout\components\SearchTips.vue
 * @Description: 猜你想搜和搜索提示
-->
<template>
  <div class="search-tips"
      v-loading="loading"
      element-loading-spinner="el-icon-loading"
      element-loading-text="载入中..."
  >
    <vuescroll>
      <div class="kind-item" v-for="kindObj in searchTypes" :key="kindObj.name">
        <!-- 类别标题 -->
        <div class="kind-title" v-if="isEmpty(kindObj)">
          <span class="title-icon iconfont" :class="kindObj.icon"></span>
          <span class="title-name">{{ kindObj.name }}</span>
        </div>
        <!-- 列表结果列表 -->
        <div class="tip-list"  >
          <div class="tip-item"
              v-for="item in kindObj.list" :key="item.id"
              @click="$emit('select',{...item,type:kindObj.type})">
            <p class="tip-name" v-html="highlight(item.name)"></p>
          </div>
        </div>
      </div>
    </vuescroll>
  </div>
</template>

<script>
import { mapState } from 'vuex'
export default {
  name: "SearchTips",
  props: {
  },
  data() {
    return {
      loading: false,
      searchTypes: [
        { name: "猜你想搜", type: 1,  icon: "iconfont icon-search", list: [] },
        { name: "单曲", type: 1, icon: "icon-musicfill", list: [] },
        { name: "歌手", type: 100, icon: "icon-user", list: [] },
        { name: "专辑", type: 10,icon: "icon-yinle", list: [] },
        { name: "歌单", type: 1000, icon: "icon-unorderedlist", list: [] },
      ],
    };
  },
  computed: {
    ...mapState('search',["kw" ]),
  },
  watch: {
    kw: {
      immediate: true,
      handler(val) {
        if (val) this.searchDone()
      },
    },
  },
  components: {},
  methods: {
    async searchSuggest() {
      let { result } = await this.$http(`/search/suggest?keywords=${this.kw}`);
      return  result
    },
    // 猜你想搜
    async searchGuess() {
      let  { result:{songs}}= await this.$http(`/cloudsearch?keywords=${this.kw}&type=1&limit=10`);
      return songs 
    },
    async searchDone(){
      this.loading = true
     let songsGuess = await this.searchGuess()
     let   { albums ,artists,playlists,songs }= await this.searchSuggest()
     this.searchTypes[0].list = songsGuess;
     this.searchTypes[1].list = songs;
     this.searchTypes[2].list = artists;
     this.searchTypes[3].list = albums;
     this.searchTypes[4].list = playlists;
     this.loading = false
    },
    isEmpty(kindObj){
      return kindObj?.list?.length > 0
    },
    //高亮搜索关键词
    highlight(name) {
      //不区分大小写
      let nkw = this.kw.replace(/[.*+?^${}()|[\]\\]/g, "\\$&")
      let reg = new RegExp(nkw, "gi");
      //去除xxs攻击
      let naemStr = name.replace(/</g, "&lt;").replace(/>/g, "&gt;");
      let hlhtml =  naemStr.replace(reg, `<span class="highlight">${nkw}</span>`);
      return hlhtml
    },
  },
  created() {},
  mounted() {},
};
</script>
<style scoped lang='scss'>
.search-tips {
  width: 100%;
  height: 100%;
  color: #666;
  padding: 5px 0 15px 0;
  background-color: #fff;
  .kind-item {
    margin-bottom: 16px;
    //最后一个不要margin-bottom
    &:nth-child(4) {
      margin-bottom: 0;
    }
    .kind-title {
      font-size: 16px;
      color: #999;
      display: flex;
      align-items: center;
      margin-bottom: 10px;
      padding: 0 15px;
      user-select: none;
      .title-icon {
        font-size: 18px;
        margin-right: 4px;
      }
    }
    .tip-list {
      font-size: 12px;
      cursor: pointer;
      .tip-item{
        height: 36px;
        line-height: 36px;
        padding-right:15px;
        padding-left:38px;
        &:hover {
          background-color: #f5f5f5;
        }
        .tip-name{
          display:flex;
          align-items:center;
        }
      }
    }
  }
}
</style>