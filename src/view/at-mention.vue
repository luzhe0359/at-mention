<template>
  <div id="at">
    <!-- state & action -->
    <van-row>
      <van-col span="24">是否为 中文输入法状态：{{isChineseInputMethod}}</van-col>
      <van-col span="24">是否为 点击@按钮打开菜单弹框：: {{isClickMention}}</van-col>
    </van-row>
    <van-row>
      <van-col span="24">
        打开@菜单弹框：
        <van-button
          type="info"
          size="small"
          @click="openPopup"
        >Show @ Menu</van-button>
      </van-col>
      <van-col span="24">
        以编程方式@某人：
        <van-button
          type="warning"
          size="small"
          @click="insertItem(userList[0])"
        >Add Mention Programmatically</van-button>
      </van-col>
    </van-row>
    <!-- editor -->
    <quill-editor
      class="editor"
      ref="myQuillEditor"
      v-model="content"
      :options="editorOption"
      @compositionstart.native="onCompositionstart"
      @compositionend.native="onCompositionend"
      @keydown.native="onKeyDownInput"
      @keyup.native="onKeyUpInput"
    />
    <div id="toolbar"></div>
    <!-- userList -->
    <van-popup
      id="edit"
      v-model="show"
      round
      position="bottom"
      :style="{ height: '40%' }"
      @close="closePopup"
    >
      <van-search
        v-model="search"
        placeholder="请输入搜索关键词"
        @search="onSearch"
      >
        <template #action>
          <van-button
            type="warning"
            size="small"
            @click="onSearch"
          >搜索</van-button>
        </template>
      </van-search>
      <van-cell-group>
        <van-cell
          :title="`编号为：${item.id}`"
          :value="`用户名为：${item.name}`"
          v-for="item in userList"
          :key="item.id"
          @click="selectItem(item)"
        />
      </van-cell-group>
    </van-popup>
  </div>
</template>

<script>
import { quillEditor } from 'vue-quill-editor'
import 'quill/dist/quill.snow.css'
import 'quill-mention'

export default {
  name: 'at-mention',
  components: {
    quillEditor
  },
  data () {
    return {
      content: '',
      isClickMention: false, // 是否点击@按钮打开@菜单弹框
      isChineseInputMethod: false, // 是否中文输入法状态
      show: false, // 弹框显示状态
      editorOption: {
        placeholder: '说点什么呢。。。',
        modules: {
          toolbar: {
            container: '#toolbar',
          },
          history: { // history 配置（History模块负责处理Quill的撤销和重做）
            delay: 0 // 将延迟设置为0，那么几乎每次输入字符都会被记录成一个变化，然后，撤销动作就会一次撤销一个字符
          },
          mention: { // 将 quill-mention 配置传递给 quill
            allowedChars: /^[A-Za-z\sÅÄÖåäö]*$/,
            mentionDenotationChars: ["@", "#"],
            // spaceAfterInsert: false,
            // fixMentionsToQuill: true, // 设置为 true 时，提及菜单将呈现在 quill 容器的上方或下方。否则，提及菜单将跟踪外延字符；
            // defaultMenuOrientation: 'bottom', // 选项是'bottom'和'top'。Quill-mention 将尝试在编辑器上方或下方呈现菜单。
            // // showDenotationChar: true // 是否打开@符号, 默认true
            // positioningStrategy: "fixed", // 选项是'normal'和'fixed'。时'fixed'，菜单将附加到正文并使用固定定位。
            // dataAttributes: [['id', 'value', 'denotationChar', 'link', 'target', 'disabled', 'time']],
            source: function (searchTerm, renderList, mentionChar) {
              console.log(searchTerm, renderList, mentionChar)
              renderList([], searchTerm)
            }
          }
        }
      },
      search: '', // 搜索人名
      userList: [ // 用户列表
        { name: '小张', id: '001' },
        { name: '小李', id: '002' },
        { name: '小安', id: '003' },
        { name: '小发', id: '004' },
        { name: '小刚', id: '005' },
      ]
    }
  },
  computed: {
    editor () {
      return this.$refs.myQuillEditor.quill
    }
  },
  methods: {
    // 中文输入触发
    onCompositionstart () {
      this.isChineseInputMethod = true
    },
    // 中文输入关闭
    onCompositionend () {
      this.isChineseInputMethod = false
    },
    // 键盘按下
    onKeyDownInput (e) {
      // 判断状态非中文输入法，并且监听到了@的事件
      if (!this.isChineseInputMethod && this.isAtCode(e)) {
        // 输入@打开@菜单弹框
        this.show = true;
      }
    },
    // 键盘弹起
    onKeyUpInput (e) {
      // 判断状态非中文输入法，并且监听到了@的事件
      if (!this.isChineseInputMethod && this.isAtCode(e)) {
        // 隐藏虚拟键盘
        this.editor.blur()
      }
    },
    // 判断设备是否为 Mobile （注意，请勿在浏览器调试模式下，切换为手机模式，否则会导致bug）
    isMobile () {
      return navigator.userAgent.match(/(iPhone|iPod|Android|ios|iOS|iPad|Backerry|WebOS|Symbian|Windows Phone|Phone)/i)
    },
    // 判断按键是否为 @
    isAtCode (e) {
      return this.isMobile() ?
        ((e.keyCode === 229 || e.keyCode === 50) && e.code === 'Digit2' && e.key === '@')
        : (((e.keyCode === 50 && e.key === '@') || (e.keyCode === 229 && e.code === 'Digit2')) && e.shiftKey)
    },
    // 点击@按钮打开@菜单弹框
    openPopup () {
      this.show = true
      this.isClickMention = true
    },
    // 关闭@菜单弹框
    closePopup () {
      this.show = false
      this.search = ''
      this.isClickMention = false
    },
    // 搜索用户
    onSearch (value) {
      this.userList = new Array(10).fill(5).map((item, index) => {
        return {
          name: `${value} - ${index}`,
          id: index
        }
      })
    },
    // 选择要@的用户
    selectItem (item) {
      // 如果不是通过@按钮打开的弹框，则需要删除多余的@符号
      if (!this.isClickMention) {
        // 撤销一步，删除手动输入的@符号（需在modules中配置history: { delay: 0 }）
        this.undo()
      }
      this.insertItem(item)
      this.show = false
    },
    // 插入@内容
    insertItem (item) {
      const mention = this.editor.getModule('mention')
      mention.insertItem({ id: item.id, value: item.name, denotationChar: '@' }, true)
    },
    // 撤销
    undo () {
      this.editor.history.undo();
    },
  }
}
</script>

<style >
#at {
  padding: 10px;
}

/* quill */
.quill-editor {
  height: 200px;
}

/* quill-toolbar */
.ql-toolbar {
  padding: 0 !important;
  border: 0 !important;
}

/* @内容 */
.mention {
  color: #24afd6;
}

.van-col {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}
</style>