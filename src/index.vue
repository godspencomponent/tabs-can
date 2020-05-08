<template>
  <div class="component">
    <ar-tabs @input='onChange' :tab-position="position">
      <ar-tab-pane v-for="(t, i) in tabs" :key="i">
        <slot :name="'slot'+i"></slot>
        <div slot="label" :style="{ color: index == i ? activeColor : color }">{{t.title}}</div>
      </ar-tab-pane>
    </ar-tabs>
  </div>
</template>

<script>
  import {VueExtend} from 'godspen-lib'
  import ArTabs from 'element-ui/lib/tabs'
  import ArTabPane from 'element-ui/lib/tab-pane'

  if (!/^edit[oe]r|preview$/i.test(window.EDIT_TYPE)) {
    require('element-ui/lib/theme-chalk/tabs.css')
    require('element-ui/lib/theme-chalk/tab-pane.css')
  }

  export default {
    mixins: [VueExtend.mixin],
    name: 'tabs-can',
    label: process.env.LABEL,
    style: process.env.STYLE,
    slots: { style: { position: 'relative' } },
    props: {
      position: {
        type: String,
        editor: {
          type: 'enum',
          label: '位置',
          defaultList: [
            'left',
            'top',
            'right',
            'bottom'
          ]
        },
        default: 'left'
      },
      activeColor: {
        type: String,
        editor: {
          label: '标签文本高亮颜色',
          type: 'color',
        },
        default: '#FF9800',
      },
      color: {
        type: String,
        editor: {
          label: '标签文本颜色',
          type: 'color',
        },
        default: '#666666',
      },
      tabs: {
        type: Array,
        editor: {
          ignore: true
        },
        default () {
          return [
            { title: '标签 1', fn: [] }
          ]
        }
      }
    },
    components: { ArTabs, ArTabPane },
    data () {
      return {
        index: 0,
      }
    },
    computed: {
    },
    watch: {
      activeColor: {
        handler (val) {
          this.$nextTick(() => this.setBarColor(val))
        },
        immediate: true
      }
    },
    methods: {
      onChange (index) {
        this.index = index
        const tab = this.tabs[index] || {}
        if (tab.fn) this.oncallExecute(tab.fn, [tab, index])
      },
      setBarColor (color) {
        const $bar = this.$el.querySelector('.el-tabs__nav > .el-tabs__active-bar')
        if (!$bar) return
        $bar.style.backgroundColor = color
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" type="text/stylus" scoped>
  .component {
    width: 100%;
    height: 100%;
    >>> .el-tabs .el-tabs__header {
      .el-tabs__item {
        font-size: inherit;
        line-height 2em;
        height 2em;
        padding-right: 20px;
        padding-left: 20px;
      }
      .el-tabs__nav-wrap:after {
        background-color: #e4e7ed;
        box-shadow: none;
      }
    }
  }
</style>
