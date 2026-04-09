<template>
  <div @click="onClick" class="horizontal-card">
    <div class="img-wrap">
        <img v-lazy="$utils.genImgUrl(img, 50)" />
    </div>
    <div class="content">
      <div class="name">{{ name }}</div>
      <div class="desc">
        <Icon :size="12" color="shallow" type="play" />
        <p class="count">{{$utils.formatNumber(count)}}</p>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
/**
 * 左边是图片 右边上下两行文字的卡片
 */
export default {
  name: "SongListCard",
  props: ["img", "name", "desc", "count"],
  methods: {
    onClick(e) {
      this.$emit("click", e)
    }
  }
}
</script>

<style lang="scss" scoped>
.horizontal-card {
  display: flex;
  padding: 0 4px;
  cursor: pointer;

  &:hover {
    background: var(--light-bgcolor);
  }

  .img-wrap {
    position: relative;
    @include img-wrap(50px);

    img {
      border-radius: 4px;
    }
  }

  .content {
    display: flex;
    flex-direction: column;
    justify-content: center;
    margin-left: 8px;
    overflow: hidden;

    .name {
      margin-bottom: 4px;
      font-size: $font-size-medium-sm;
      @include text-ellipsis();
    }

    .desc {
      font-size: $font-size-sm;
      color: var(--font-color-grey-shallow);
      @include text-ellipsis();
      display: flex;
      align-items: center;
      .count {
            margin-left: 4px;
          }
    }
  }
}
</style>
