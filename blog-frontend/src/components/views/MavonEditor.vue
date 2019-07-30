<template>
  <div id="mavon-editor" :class="theme">
    <div class="operate">
      <div class="editor-area">
        <el-form :model="newComment" label-width="0px" ref="commentForm">
          <el-form-item>
            <iv-row :gutter="15">
              <iv-col :xs="8" :sm="8" :md="6" :lg="6">
                <iv-input v-model="newComment.name" placeholder="请输入您的昵称" size="large">
                  <span slot="prepend">昵称 </span>
                </iv-input>
              </iv-col>
              <iv-col :xs="16" :sm="16" :md="12" :lg="11">
                <iv-input v-model="newComment.email" placeholder="联系方式以评论" size="large">
                  <span slot="prepend">邮箱 </span>
                </iv-input>
              </iv-col>
            </iv-row>
          </el-form-item>
          &nbsp;
          <el-form-item>
            <iv-row :gutter="15">
              <iv-col :xs="16" :sm="16" :md="12" :lg="11">
              <iv-input v-model="newComment.content" placeholder="评论文章" size="large">
                <span slot="prepend">评论 </span>
              </iv-input>
              </iv-col>
              <iv-col :xs="16" :sm="8" :md="6" :lg="6">
                <iv-button size="large" @click="publish" :type="buttonType" :disabled="editable">发布</iv-button>
              </iv-col>
            </iv-row>
<!--            <mavon-editor class="editor-area"-->
<!--                          style="height: 100%; min-height: 50px; min-width: 200px; z-index: 9; max-height: 200px;"-->
<!--                          :toolbarsFlag="toolbarsFlag"-->
<!--                          :subfield="subfield"-->
<!--                          :placeholder="placeholder"-->
<!--                          :toolbars="toolbars"-->
<!--                          :editable="editable"-->
<!--                          v-model="comment.content"></mavon-editor>-->
          </el-form-item>
        </el-form>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
import MavonEditor from 'mavon-editor'
import 'mavon-editor/dist/css/index.css'

export default {
  props: {
    theme: {
      Type: String,
      default: ''
    },
    subfield: {
      default: true
    },
    placeholder: {
      default: '输入评论内容...'
    },
    toolbarsFlag: {
      default: true
    },
    editable: true
  },
  data () {
    return {
      newComment: {
        content: '',
        name: '',
        email: ''
      },
      valueChanged: false,
      toolbars: {
        bold: true, // 粗体
        italic: true, // 斜体
        header: true, // 标题
        underline: true, // 下划线
        strikethrough: true, // 中划线
        mark: true, // 标记
        superscript: true, // 上角标
        subscript: true, // 下角标
        quote: true, // 引用
        ol: true, // 有序列表
        ul: true, // 无序列表
        link: true, // 链接
        imagelink: true, // 图片链接
        code: true, // code
        table: true, // 表格
        fullscreen: true, // 全屏编辑
        readmodel: false, // 沉浸式阅读
        htmlcode: false, // 展示html源码
        help: false, // 帮助
        /* 1.3.5 */
        undo: false, // 上一步
        redo: false, // 下一步
        trash: true, // 清空
        save: false, // 保存（触发events中的save事件）
        /* 1.4.2 */
        navigation: false, // 导航目录
        /* 2.1.8 */
        alignleft: true, // 左对齐
        aligncenter: true, // 居中
        alignright: true, // 右对齐
        /* 2.2.1 */
        subfield: true, // 单双栏模式
        preview: true // 预览
      }
    }
  },
  computed: {
    buttonType: function () {
      return this.theme === 'dark-theme' ? 'warning' : 'primary'
    }
  },
  name: 'editor',
  components: {
    'mavon-editor': MavonEditor.mavonEditor
  },
  methods: {
    change (value) {
      if (value.length > 0) {
        if (!this.valueChanged) {
          this.$emit('valueChanged', true)
          this.valueChanged = true
          this.toolbarsFlag = true
          this.toolbars['navigation'] = true
          this.listenWindowWidth()
        }
      } else {
        if (this.valueChanged) {
          this.$emit('valueChanged', false)
          this.valueChanged = false
          this.toolbarsFlag = false
          this.toolbars['navigation'] = false
          this.listenWindowWidth()
        }
      }
    },
    listenWindowWidth () {
      // 此方法用于监听窗口宽度变化,改变编辑器菜单
      var clientWidth = document.documentElement.clientWidth
      if (clientWidth < 900) {
        this.$set(this.toolbars, 'readmodel', false)
        this.$set(this.toolbars, 'htmlcode', false)
        this.$set(this.toolbars, 'navigation', false)
        this.$set(this.toolbars, 'subfield', false)
      } else {
        this.$set(this.toolbars, 'readmodel', true)
        this.$set(this.toolbars, 'htmlcode', true)
        this.$set(this.toolbars, 'navigation', true)
        this.$set(this.toolbars, 'subfield', true)
      }
    },
    publish () {
      if (this.newComment.name === null || this.newComment.name === '') {
        this.$Message.error('昵称不能为空')
      } else if (this.newComment.email === null || this.newComment.email === '') {
        this.$Message.error('邮箱不能为空')
      } else {
        this.newComment.commentLevel = 0
        if (this.$route.params.bookId) {
          this.newComment.linkId = this.$route.params.bookId
          this.newComment.type = 1
        }
        if (this.$route.params.bookNoteId) {
          this.newComment.linkId = this.$route.params.bookNoteId
          this.newComment.type = 2
        }
        if (this.$route.params.articleId) {
          this.newComment.linkId = this.$route.params.articleId
          this.newComment.type = 0
        }
        this.$http({
          url: this.$http.adornUrl('/comment/save'),
          data: this.$http.adornData(this.newComment),
          method: 'post'
        }).then(({data}) => {
          if (data && data.success) {
            this.$Message.success('评论成功')
            location.reload()
          } else {
            this.$Message.error('评论失败')
          }
        })
      }
    }
  },
  mounted () {
    var that = this
    window.onresize = function temp () {
      that.listenWindowWidth()
    }
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/theme.styl";

  #mavon-editor
    height 100%
    width 100%
    display flex
    flex-direction column

    .operate
      margin-bottom 15px

      .iv-dropdown-link
        display block
        height 36px
        line-height 36px
        text-align right
        font-size 15px
        color $color-main-primary

        &:hover
          cursor pointer

    .editor-area
      position relative
      flex 1
      padding 2px
      height 100%
      min-height 50px
      min-width 200px

    .bottom-area
      flex 0 0 40px
      height 40px
      display flex
      padding-top 15px
      justify-content: space-between

    &.dark-theme
      .operate
        margin-bottom 15px

        .iv-dropdown-link
          display block
          height 36px
          line-height 36px
          text-align right
          font-size 15px
          color $color-gradually-gray-61

          &:hover
            color $color-secondary-warning
            border-bottom 2px solid $color-secondary-warning
            cursor pointer

      .bottom-area
        .comment-tip
          a
            color $color-gradually-gray-61

            &:hover
              color $color-secondary-warning

</style>
