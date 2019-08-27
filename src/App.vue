<template>
  <div id="app">
    <StyleEditor ref="styleEditor" :code="currentStyle"></StyleEditor>
    <ResumeEditor ref="resumeEditor" :markdown="currentMarkdown" :enableHtml="enableHtml"></ResumeEditor>
  </div>
</template>

<script>
  import StyleEditor from './components/StyleEditor'
  import ResumeEditor from './components/ResumeEditor'
  import './assets/reset.css'

  export default {
    name: 'app',
    components: {
      StyleEditor,
      ResumeEditor
    },
    data() {
      return {
        interval: 40,
        currentStyle: '',
        enableHtml: false,
        fullStyle: [
          `/*
* Inspired by http://strml.net/
* 您好，我叫张锋。一位在寻找Java方面工作的程序员
* 首先感谢您百忙之中能够点开我的这份动态简历
* 下面我想通过这种方式来介绍一下我自己
* 这个页面看着不舒服，我先稍微改下页面格式
*/

/* 首先给所有元素加上过渡效果 */
* {
  transition: all .3s;
}
/* 白色背景太单调了，来点背景 */
html {
  color: rgb(222,222,222); background: rgb(0,43,54);
}
/* 文字离边框太近了 */
.styleEditor {
  padding: .5em;
  border: 1px solid;
  margin: .5em;
  overflow: auto;
  width: 45vw; height: 90vh;
}
/* 代码高亮 */
.token.selector{ color: rgb(133,153,0); }
.token.property{ color: rgb(187,137,0); }
.token.punctuation{ color: yellow; }
.token.function{ color: rgb(42,161,152); }

/* 加点 3D 效果 */
html{
  perspective: 1000px;
}
.styleEditor {
  position: fixed; left: 0; top: 0;
  -webkit-transition: none;
  transition: none;
  -webkit-transform: rotateY(10deg) translateZ(-100px) ;
          transform: rotateY(10deg) translateZ(-100px) ;
}

/* 接下来我给自己准备一个编辑器 */
.resumeEditor{
  position: fixed; right: 0; top: 0;
  padding: .5em;  margin: .5em;
  width: 48vw; height: 90vh;
  border: 1px solid;
  background: white; color: #222;
  overflow: auto;
}
/* 好了，终于可以写简历了，您久等了！ */


`,
          `
/* 这个简历好像差点什么
 * 对了，这是 Markdown 格式的，但界面看起来不是很友好
 * Emm………，可以用开源工具翻译成 HTML
 */
`
          ,
          `
/* 再对 HTML 加点样式 */
.resumeEditor{
  padding: 2em;
}
.resumeEditor h2{
  display: inline-block;
  border-bottom: 1px solid;
  margin: 1em 0 .5em;
}
.resumeEditor ul,.resumeEditor ol{
  list-style: none;
}
.resumeEditor ul> li::before{
  content: '•';
  margin-right: .5em;
}
.resumeEditor ol {
  counter-reset: section;
}
.resumeEditor ol li::before {
  counter-increment: section;
  content: counters(section, ".") " ";
  margin-right: .5em;
}
.resumeEditor blockquote {
  margin: 1em;
  padding: .5em;
  background: #ddd;
}
`],
        currentMarkdown: '',
        fullMarkdown: `张锋
----

2017年毕业于江苏科技大学，专业是软件工程，是位初级程序员，主攻Java方面，现在在寻找相关方面的工作。
求职方向：**Java开发工程师**

技能
----

- 编程语言：Java、C++、HTML5+CSS3+JavaScript/JQuery；
- Web框架：Spring、SpringMVC、Strut2、Hibernate、Mybatis；
- 版本管理和项目构建工具：Git、Maven；
- 开发工具：熟练使用IntelliJ IDEA、MyEclipse10、Eclipse进行项目构建、版本控制、开发和调试；
- 数据库和操作系统：熟悉MySQL/SQL Server的开发，了解基本的Linux命令和shell脚本。

项目经验
----

1. APP信息管理平台
  一个企业级的CMS系统，是针对Android应用市场开发的后台管理平台，可以对APP进行维护管理。
2. 峰星网上商城
  系统采用SSH框架，按照MVC设计模式，实现购物车管理、单点登录、订单管理模块功能。

具体功能和所用的技术写在静态简历里了。
“我确信已发现了一种美妙的写法，可惜这里空白的地方太小，写不下。”┑(￣Д ￣)┍~

个人总结
----

- 自我定位: 在深度方面钻研后端，比如阅读Java源码、框架源码，进行程序性能调优和学习高并发、分布式等技术，同时也要拓宽自己的广度，多了解其他方面。
- 工作态度: 高效完成自己的本职工作，具有良好的编码规范、良好的沟通表达能力和文档交付能力。
- 解决问题: 首先思考问题产生的根源，尽量从源头入手，然后利用Google、Stackoverflow等等搜索寻找答案，如果已有解决方案就不再造轮子。或者阅读源码、API文档，在REPL中实践解决，之后总结分享出去。
- 优势: 热爱技术、自学能力强,善于总结和反思。拥有开阔的视野和良好的心态.
- 劣势: 对待一件事情有时会过于追求完美，钻牛角尖。

链接
----

- [GitHub](https://github.com/linut)
- [我的博客](https://blog.csdn.net/fenzang)

`
      }
    },
    created() {
      this.makeResume()
    },

    methods: {
      makeResume: async function () {
        await this.progressivelyShowStyle(0)
        await this.progressivelyShowResume()
        await this.progressivelyShowStyle(1)
        await this.showHtml()
        await this.progressivelyShowStyle(2)
      },
      showHtml: function () {
        return new Promise((resolve, reject) => {
          this.enableHtml = true
          resolve()
        })
      },
      progressivelyShowStyle(n) {
        return new Promise((resolve, reject) => {
          let interval = this.interval
          let showStyle = (async function () {
            let style = this.fullStyle[n]
            if (!style) { return }
            // 计算前 n 个 style 的字符总数
            let length = this.fullStyle.filter((_, index) => index <= n).map((item) => item.length).reduce((p, c) => p + c, 0)
            let prefixLength = length - style.length
            if (this.currentStyle.length < length) {
              let l = this.currentStyle.length - prefixLength
              let char = style.substring(l, l + 1) || ' '
              this.currentStyle += char
              if (style.substring(l - 1, l) === '\n' && this.$refs.styleEditor) {
                this.$nextTick(() => {
                  this.$refs.styleEditor.goBottom()
                })
              }
              setTimeout(showStyle, interval)
            } else {
              resolve()
            }
          }).bind(this)
          showStyle()
        })
      },
      progressivelyShowResume() {
        return new Promise((resolve, reject) => {
          let length = this.fullMarkdown.length
          let interval = this.interval
          let showResume = () => {
            if (this.currentMarkdown.length < length) {
              this.currentMarkdown = this.fullMarkdown.substring(0, this.currentMarkdown.length + 1)
              let lastChar = this.currentMarkdown[this.currentMarkdown.length - 1]
              let prevChar = this.currentMarkdown[this.currentMarkdown.length - 2]
              if (prevChar === '\n' && this.$refs.resumeEditor) {
                this.$nextTick(() => this.$refs.resumeEditor.goBottom())
              }
              setTimeout(showResume, interval)
            } else {
              resolve()
            }
          }
          showResume()
        })
      }
    }
  }

</script>

<style scoped>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }

  html {
    min-height: 100vh;
  }
  *{
    box-sizing: border-box;
  }
</style>
