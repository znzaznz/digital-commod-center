<script>
export default {
  onLaunch: function () {
    console.log('App Launch')
  },
  onShow: function () {
    console.log('App Show')
  },
  onHide: function () {
    console.log('App Hide')
  }
}
</script>

<style>
@import url("./static/common/iconfont.css");

/* 定义基础类名 */
.iconfont {
  font-family: "iconfont" !important;
  font-size: 32px;
  font-style: normal;
}


/* 不要用 *，改为这一串 */
view, scroll-view, text, image, button, checkbox, input, label, picker, radio, switch, textarea {
  box-sizing: border-box;
}

/*每个页面公共css */
</style>
