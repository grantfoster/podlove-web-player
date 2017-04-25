<template>
  <div class="podlove-chapters--entry" :style="chapterStyle(theme, chapter)" @click="onChapterClick(chapter)">
    <span class="index">{{index + 1}}</span>
    <span class="title truncate">{{chapter.title}}</span>
    <span class="timer">{{remainingTime(chapter, playtime)}}</span>
    
    <span class="progress" :style="progressStyle(theme, chapter, playtime)"></span>
  </div>
</template>

<script>
  import color from 'color'
  import store from 'store'
  import {secondsToTime} from 'utils/time'

  const chapterStyle = (theme, chapter) => {
    const style = {}

    if (chapter.active) {
      style['background-color'] = color(theme.tabs.body.backgroundActive).fade(0.9)
      style['color'] = theme.tabs.body.textActives
    }

    return style
  }

  const progressStyle = (theme, chapter, playtime) => {
    if (!chapter.active || playtime > chapter.end) {
      return {}
    }

    let progress = ((playtime - chapter.start) * 100) / (chapter.end - chapter.start)

    return {
      'width': progress + '%',
      'background-color': theme.tabs.body.progress
    }
  }

  const remainingTime = (chapter, playtime) => {
    if (chapter.active) {
      return secondsToTime(chapter.end - playtime)
    }

    return secondsToTime(chapter.end - chapter.start)
  }

  const onChapterClick = chapter => {
    store.dispatch(store.actions.updatePlaytime(chapter.start))
    store.dispatch(store.actions.setPlaytime(chapter.start))
  }

  export default {
    data() {
      return {
        theme:      this.$select('theme'),
        chapters:   this.$select('chapters'),
        playtime:   this.$select('playtime'),
        timerMode:  this.$select('timerMode')
      }
    },
    methods: {
      chapterStyle,
      progressStyle,
      remainingTime,
      onChapterClick
    },
    props: ['chapter', 'index']
  }
</script>

<style lang="scss">
  @import 'variables';
  @import 'font';

  .podlove-chapters--entry {
    width: 100%;
    position: relative;
    padding: $padding / 2 $padding;
    font-weight: 300;
    display: flex;
    cursor: pointer;

    .index {
      display: block;
      width: 30px;
    }

    .title {
      display: block;
      width: calc(100% - 30px - 30px)
    }

    .timer {
      display: block;
      text-align: right;
      @include font-monospace();
    }

    .progress {
      position: absolute;
      left: 0;
      bottom: 0;
      height: 3px;
    }
  }
</style>