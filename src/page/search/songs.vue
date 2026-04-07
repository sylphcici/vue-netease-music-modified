// 搜索详情页面
<template>
  <div class="search-songs">
    <WithPagination
      :getData="getSearch"
      :getDataParams="searchParams"
      :limit="30"
      :scrollTarget="searchRoot.$refs && searchRoot.$refs.header"
      :total="songCount"
      @getDataSuccess="onGetSearch"
    >
      <div class="table">
        <SongTable
          :highlightText="keywords"
          :renderNameDesc="renderNameDesc"
          :songs="songs"
          :stripe="true"
        />
      </div>
    </WithPagination>
  </div>
</template>

<script type="text/ecmascript-6">
import { getSearch } from "@/api"
import SongTable from "@/components/song-table"
import WithPagination from "@/components/with-pagination"
import { createSong } from "@/utils"
import { getSongDetail } from "../../api/song";

export default {
  inject: ["searchRoot"],
  created() {
    this.getSearch = getSearch
  },
  data() {
    return {
      songs: [],
      songCount: 0,
      currentPage: 1
    }
  },
  methods: {
    async onGetSearch(result) {
      const {
        result: { songs, songCount }
      } = result
      const ids = songs.map(song => song.id).join(',')
      const detailRes = await getSongDetail(ids)
      const detailSongs = detailRes.songs || []
      this.songs = songs.map(song => {
        const { id, mvid, name, alias, artists, duration, album } = song
        const target = detailSongs.find(item => item.id === id)
        let picUrl = ''
        if (target && target.al && target.al.picUrl) {
          picUrl = target.al.picUrl
        }
        return createSong({
          id,
          name,
          alias,
          artists,
          duration,
          mvId: mvid,
          albumName: album.name,
          albumId: album.id,
          img: picUrl
        })
      })
      this.songCount = songCount
      this.searchRoot.onUpdateCount(songCount)
    },
    renderNameDesc(scope) {
      const { alias } = scope.row
      return alias.map(desc => (
        <HighlightText
          class="name-desc"
          text={desc}
          highlightText={this.keywords}
        />
      ))
    }
  },
  computed: {
    keywords() {
      return this.searchRoot.keywords
    },
    searchParams() {
      return { keywords: this.keywords }
    }
  },
  components: {
    SongTable,
    WithPagination
  }
}
</script>

<style lang="scss" scoped>
.search-songs {
  .table {
    .name-desc {
      display: block;
      margin-top: 8px;
      color: var(--font-color-grey-shallow);
      @include text-ellipsis;
    }
  }

  .pagination {
    margin-top: 16px;
    text-align: right;
  }
}
</style>
