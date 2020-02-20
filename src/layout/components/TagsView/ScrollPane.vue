<template>
  <div class="tags-box">
    <el-button
      class="scroll-arrow"
      icon="el-icon-arrow-left"
      @click="handleArrow(-80)"
    />
    <el-scrollbar
      ref="scrollContainer"
      :vertical="false"
      class="scroll-container"
      @wheel.native.prevent="handleScroll"
    >
      <slot />
    </el-scrollbar>
    <el-button
      class="scroll-arrow"
      icon="el-icon-arrow-right"
      @click="handleArrow(80)"
    />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'

const tagSpacing = 4

@Component({
  name: 'ScrollPane'
})
export default class extends Vue {
  private handleScroll(e: MouseWheelEvent) {
    const eventDelta = (e as any).wheelDelta || -e.deltaY * 40
    const scrollContainer = this.$refs.scrollContainer as Vue
    const scrollWrapper = scrollContainer.$refs.wrap as HTMLElement
    scrollWrapper.scrollLeft = scrollWrapper.scrollLeft + eventDelta / 4
  }

  private handleArrow(eventDelta: number) {
    const scrollContainer = this.$refs.scrollContainer as Vue
    const scrollWrapper = scrollContainer.$refs.wrap as HTMLElement
    scrollWrapper.scrollLeft = scrollWrapper.scrollLeft + eventDelta
  }

  public moveToTarget(currentTag: HTMLElement) {
    const scrollContainer = this.$refs.scrollContainer as Vue
    const container = scrollContainer.$el as HTMLElement
    const containerWidth = container.offsetWidth
    const scrollWrapper = scrollContainer.$refs.wrap as HTMLElement
    const tagList = this.$parent.$refs.tag as any[]

    let firstTag = null
    let lastTag = null

    // find first tag and last tag
    if (tagList.length > 0) {
      firstTag = tagList[0]
      lastTag = tagList[tagList.length - 1]
    }

    if (firstTag === currentTag) {
      scrollWrapper.scrollLeft = 0
    } else if (lastTag === currentTag) {
      scrollWrapper.scrollLeft = scrollWrapper.scrollWidth - containerWidth
    } else {
      // find preTag and nextTag
      const currentIndex = tagList.findIndex(item => item === currentTag)
      const prevTag = tagList[currentIndex - 1]
      const nextTag = tagList[currentIndex + 1]
      // the tag's offsetLeft after of nextTag
      const afterNextTagOffsetLeft = nextTag.$el.offsetLeft + nextTag.$el.offsetWidth + tagSpacing
      // the tag's offsetLeft before of prevTag
      const beforePrevTagOffsetLeft = prevTag.$el.offsetLeft - tagSpacing

      if (afterNextTagOffsetLeft > scrollWrapper.scrollLeft + containerWidth) {
        scrollWrapper.scrollLeft = afterNextTagOffsetLeft - containerWidth
      } else if (beforePrevTagOffsetLeft < scrollWrapper.scrollLeft) {
        scrollWrapper.scrollLeft = beforePrevTagOffsetLeft
      }
    }
  }
}
</script>

<style lang="scss">
.scroll-container {
  .el-scrollbar__bar {
    bottom: 0px;
  }

  .el-scrollbar__wrap {
    height: 49px;
  }
}
</style>

<style lang="scss" scoped>
.scroll-container {
  white-space: nowrap;
  position: relative;
  overflow: hidden;
  width: 100%;
}
.tags-box {
  display: flex;
  flex-direction: row;
  align-items: flex-start;
}
.scroll-arrow {
  width: 30px;
  height: 30px;
  margin: 2px 2px;
  padding: 1px 1px;

}
</style>
