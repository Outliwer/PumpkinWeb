<template>
  <div class="common-footer">
    <p class="copyright">
      <a href="http://www.beian.miit.gov.cn/" target="_blank">{{ siteInfo.icp }}</a>
      <span>|</span>
      {{ siteInfo.copyright }}
      <span>|</span>
      <span v-html="siteInfo.copyright_desc"></span>
    </p>
  </div>
</template>

<script type="text/ecmascript-6">
export default {
  data () {
    return {
      siteInfo: {
        icp: '鄂ICP备19014818号',
        copyright: '版权所有 © 2018 - 2019'
      }
    }
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/theme.styl"
  .common-footer
    padding 15px 5px
    margin-top 50px
    text-align center
    background $default-background-color
    border-top 0px solid $default-border-color
    z-index 99
  .copyright
    margin 10px
    color $default-title-color
    a
      color $default-link-color
      &:hover
        color $default-link-hover-color
</style>
