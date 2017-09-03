<template>
  <transition name="slide">
    <musci-list :songs="songs" :title="title" :bg-image="bgImage"></musci-list>
  </transition>
</template>
 <script type="text/ecmascript-6">
  import {mapGetters} from 'vuex'
  import {ERR_OK} from 'api/config'
  import {getSingerDetail} from '../../api/singer'
  import {creatSong} from '../../common/js/song'
  import musciList from '../music-list/music-list.vue'
  export default {
    data() {
      return {
        songs: []
      }
    },
    computed: {
      title() {
        return this.singer.name
      },
      bgImage() {
        console.log(this.singer)
        return this.singer.avatar
      },
      ...mapGetters([
        'singer'
      ])
    },
    created() {
      this._getDetail()
    },
    methods: {
      _getDetail() {
        if (!this.singer.id) {
          this.$router.push('/singer')
          return false
        }
        getSingerDetail(this.singer.id).then((res) => {
          if (res.code === ERR_OK) {
            this.songs = this._normalizeSongs(res.data.list)
          }
        })
      },
      _normalizeSongs(list) {
        let ret = []
        list.forEach((item) => {
          let {musicData} = item
          if (musicData.songid && musicData.albumid) {
            ret.push(creatSong(musicData))
          }
        })
        return ret
      }
    },
    components: {
      musciList
    }
  }
</script>
<style lang="stylus" rel="stylesheet/stylus" scoped>
  @import "~common/stylus/variable"
  .slide-enter-active,.slide-leave-active
    transition : all 0.3s 
  .slide-enter,.slide-leave-to
    transform :translate3d(100%, 0, 0)
</style>