<template>
  <div class="component-editor">
    <el-card style="background-color: #262626;">
      <div>
        <span style="color:#ddd; font-size: 16px;">标签栏</span>
      </div>
      <div v-for="(info, index) in componentInfo.tabs" :key="index" class="ar-menu-item el-icon-menu" :class="{active: itemModel === info}" @click="selectItem(info)">
        <div class="ar-menu-item-title">{{info.title | slice}}</div>
        <div class="ar-menu-item-operation">
          <i title="上移" v-show="index !== 0" @click.stop="upItem(index)" class="el-icon-xxxxx">↑</i>
          <i title="下移" v-show="index < length - 1" @click.stop="downItem(index)" class="el-icon-xxxxx">↓</i>
          <i v-show="!(length == 1)" @click.stop="removeItem(index)" title="删除" class="el-icon-delete"></i>
        </div>
      </div>
      <div @click="addItem(componentInfo.tabs)" class="add-menu-item">添加 +</div>
      <el-form size='mini' v-if="itemModel" :model='itemModel' label-position="left">
        <el-form-item label="内容" prop="title" :required="true">
          <el-input v-model="itemModel.title"></el-input>
        </el-form-item>
        <el-form-item label="回调" prop="fn">
          <attr-function :content.sync="itemModel.fn"
          ></attr-function>
        </el-form-item>
      </el-form>
    </el-card>
  </div>
</template>

<script>
  export default {
    name: 'maliangeditor',
    props: {
      componentInfo: {
        type: [Object],
        default () {
          return {
          }
        }
      }
    },
    filters: {
      slice (str, len = 6) {
        return str.slice(0, len) + (str.length > len ? '...' : '')
      }
    },
    data: function () {
      return {
        itemModel: null
      }
    },
    mounted: function () {
        if (!this.componentInfo.tabs) this.$set(this.componentInfo, 'tabs', [ this.genItem()
      ])
    },
    computed: {
      length () {
        return this.componentInfo.tabs.length
      }
    },
    methods: {
      genItem () {
        return {
          title: '标签 1',
          fn: [],
        }
      },
      addItem () {
        const item = this.genItem()
        this.componentInfo.tabs.push(item)
        this.itemModel = item
      },
      upItem (index) {
        if (index == 0) return
        this.componentInfo.tabs.splice(index - 1, 2, parent[index], parent[index-1])
      },
      downItem (index) {
        if (index == parent.length - 1) return
        this.componentInfo.tabs.splice(index, 2, parent[index + 1], parent[index])
      },
      removeItem (index) {
        this.componentInfo.tabs.splice(index, 1)
        this.itemModel = null
      },
      selectItem (item) {
        this.itemModel = item
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" type="text/stylus" scoped>
  .component-editor {
    .ar-menu-item {
      display flex
      padding 8px 10px 8px 0
      background-color: #262626;
      color: #ddd
      font-size 14px;
      &:hover, &.active {
        background-color: #121212;
      }
      .ar-menu-item-title {
        margin-left 4px;
      }
      .ar-menu-item-operation {
        flex: 1
        text-align right
        > i {
          cursor pointer
        }
      }
    }
    .add-menu-item {
      color: #ddd
      font-size 14px;
      text-align center;
      padding 8px;
      cursor pointer
      line-height 1;
      background-color: #121212;
      margin-top: 4px;
    }
  }
</style>
