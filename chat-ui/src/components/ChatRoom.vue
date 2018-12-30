<template>
  <div>
    <div v-html="content"></div>
    <input type="text" name="input" v-model="message" />
    <button @click="sendMsgToServer">发送</button>
  </div>
</template>

<script>
  export default {
    name: 'ChatRoom',
    data() {
      return {
        userName: 'test',
        content: '',
        message: '',
        webSocket: null
      }
    },
    components: {},
    methods: {
      //创建webSocket
      getWebSocket() {
        //WebSocket客户端 PS：URL开头表示WebSocket协议 中间是域名端口 结尾是服务端映射地址
        var webSocket = new WebSocket('ws://localhost:8081/chat')
        //当服务端打开连接
        webSocket.onopen = event => {
          console.log('WebSocket打开连接')
        }
        //当服务端发来消息：1.广播消息 2.更新在线人数
        webSocket.onmessage = event => {
          console.log('WebSocket收到消息：%c' + event.data, 'color:green')
          //获取服务端消息
          var message = JSON.parse(event.data) || {}
          //展示
          if (message.type === 'SPEAK') {
            this.content +=
              '<div class="mdui-card" style="margin: 10px 0;">' +
              '<div class="mdui-card-primary">' +
              '<div class="mdui-card-content message-content">' +
              message.username +
              '：' +
              message.msg +
              '</div>' +
              '</div></div>'
          }
          //防止刷屏
          //   var $cards = $messageContainer
          //     .children('.mdui-card:visible')
          //     .toArray()
          //   if ($cards.length > 5) {
          //     $cards.forEach(function(item, index) {
          //       index < $cards.length - 5 && $(item).slideUp('fast')
          //     })
          //   }
        }
        //关闭连接
        webSocket.onclose = event => {
          console.log('WebSocket关闭连接')
        }
        //通信失败
        webSocket.onerror = event => {
          console.log('WebSocket发生异常')
        }
        return webSocket
      },
      // 发送消息
      sendMsgToServer() {
        if (this.message) {
          this.webSocket.send(
            JSON.stringify({
              username: this.userName,
              msg: this.message
            })
          )
          this.message = ''
        }
      }
    },
    mounted() {
      this.webSocket = this.getWebSocket()
    }
  }
</script>

<style lang="scss" scoped></style>
