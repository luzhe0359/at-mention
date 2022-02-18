<template>
  <div>
    <van-nav-bar title="at-mention @功能" />
    <van-notice-bar
      left-icon="volume-o"
      text="基于vue2.x + vant2.x 实现的@功能(稍作修改即可支持原生js和react)，兼容pc和手机端，如有bug，欢迎提出~"
    />
    <at-mention />
    <van-notice-bar
      color="#999"
      background="#ecf9ff"
      left-icon="info-o"
      wrapable
    >
      pc端用户请注意，浏览器切换至手机调试模式时，会导致@功能无效(代码会判断当前设备为移动端)，请手动拖动浏览器窗口大小或使用手机调试。
    </van-notice-bar>
  </div>
</template>

<script>
import atMention from './at-mention'

export default {
  name: 'index',
  components: {
    atMention
  }
}
</script>