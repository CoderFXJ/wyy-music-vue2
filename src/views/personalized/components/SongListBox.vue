<!--
 * @Author: FXJ
 * @LastEditTime: 2022-10-16 16:47:59
 * @FilePath: \vue-wyy-music\src\views\personalized\components\SongListBox.vue
 * @Description: 上下结构的歌单盒子
-->

<template>
  <div class="song-list-box"  @click="toSongListDetail(algInfo.id)">
    <div class="img-box"  :class="ishover ? 'active':''"
    @mouseenter="ishover=true"  @mouseleave="ishover=false" >
      <img :src="algInfo.picUrl"   /> 
      <span class="play-number">
        <i class="iconfont icon-play"></i>
        <span class="number" v-if="algInfo.playCount">
          {{algInfo.playCount | playCountFilter}}</span>
      </span> 
      <span class="pg-count" v-if="algInfo.programCount">声音{{algInfo.programCount}}</span>
      <PlayBtn  :position="position"  @click.native.stop="addSongToList(algInfo.id)" :size="size" :bgFilter="true"/>
      <!-- 歌单创建者 -->
      <p class="creator" v-if="cname">
         <i class="iconfont icon-user"></i>
         <span class="name text-of-single">{{cname}}</span>
         <i class="iconfont icon-dengji1"></i>
      </p>
    </div>
    <p class="album-des text-of-multi" v-html="algInfo.name"></p>
    <p class="album-artist text-of-single" v-if="algInfo.artist">
       {{algInfo.artist.name}}</p>
  </div>
</template>
<script>
import page from '@/mixins/page'
import songList from '@/mixins/songList'
import { mapState, mapMutations } from 'vuex'
export default {
  name: "SongListBox",
  props: {
    algInfo: {
      type: Object,
      default: () => {},
    },
    position: {
      type: String,
      default: "br",
    },
    size: {
      type: String,
      default: "normal",
    },
    showCreator: {
      type: Boolean,
      default: false,
    },
  },
  mixins: [page,songList],
  data() {
    return {
      ishover: false,
    };
  },
  created() {},
  computed: {
    ...mapState('player',['songList']),
    cname(){
      return this.algInfo?.creator?.nickname || ''
    }
  },
  components: {},
  methods: {
    
  },
  mounted() {
  },
  watch: {},
};
</script>
<style scoped lang="scss">
.song-list-box {
  width: 100%;
  .img-box{
    //宽高相等
    width: 100%;
    border-radius: 8px;
    overflow: hidden;
    position: relative;
    cursor:pointer;
     border:1px solid #eee;
    &::after{
      content: "";
      display: block;
      padding-bottom: 100%;
    }
    img{
      position: absolute;
      width: 100%;
      height:100%;
      top: 0;
      left: 0;
    }
    .play-number{
      position: absolute;
      top: 8px;
      right: 8px;
      color: #fff;
      font-weight: 500;
      display: flex;
      align-items: center;
      vertical-align:middle;
      .icon-play{
        font-size: 14px;
      }
      .number{
        font-size: 10px;
      }
    }
    .pg-count{
      font-size: 12px;
      position: absolute;
      top: 8px;
      left: 8px;
      color: #fff;
    }
    //播放按钮
     .play-btn{
      opacity: 0;
    }
    &.active .play-btn {
     opacity: 1;
    }
    //歌单创建者
    .creator{
      position: absolute;
      bottom: 0px;
      left: 0px;
      max-width: 80%;
      overflow:hidden;
      height: 30px;
      color: #fff;
      font-size: 12px;
      display: flex;
      align-items: center;
      padding: 10px ;
      .icon-user{
        font-size:14px;
      }
      .name{
         opacity: .9;
         margin:0 4px;
         &:hover{
            opacity: 1;
         }
      }
      .icon-dengji1{
        font-size: 10px;
        background-color: #EC4141;
        opacity: 1;
        border-radius: 50%;
        width: 16px;
        height: 16px;
        display: inline-block;
        text-align: center;
        vertical-align:middle;
        padding-top:1px;
        padding-right:.5px;
      }
    }
  }
  .album-des{
    margin-top: 4px;
    font-size: 16px;
    color: #333;
    line-height: 1.5;
    text-align: left;
    cursor: pointer;
  }
  .album-artist{
    margin-top:0px;
    text-align: left;
    font-size: 14px;
    color:#888;
    cursor: pointer;
    &:hover{
      color: #555;
    }
  }
}
</style>
