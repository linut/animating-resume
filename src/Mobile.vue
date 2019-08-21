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
* 您好，我是张锋。一位在寻找Java方面工作的程序员。
* 首先感谢您百忙之中能够点开我的这份动态简历。
* 下面我想通过这种方式来介绍一下我自己。
* 哦，对了，您是不是觉得这个页面看着不舒服，那我先稍微改下页面格式。
*/

/* 首先给所有元素加上过渡效果 */
* {
  transition: all .3s;
}
/* 白色背景太单调了，那来点背景 */
html {
  color: rgb(222,222,222);
  background: rgb(0,43,54);
}
/* 文字离边框太近了 */
.styleEditor {
  padding: .5em;
  border: 1px solid;
  overflow: auto;
  width: 90vw;
  margin: 2.5vh 5vw;
  height: 90vh;
}
/* 太高了 */
.styleEditor {
  height: 45vh;
}
/* 代码高亮 */
.token.selector{
  color: rgb(133,153,0);
}
.token.property{
  color: rgb(187,137,0);
}
.token.punctuation{
  color: yellow;
}
.token.function{
  color: rgb(42,161,152);
}

/* 加点 3D 效果 */
html{
  perspective: 1000px;
}
.styleEditor {
  position: fixed; left: 0; top: 0;
  transform: rotateX(-10deg) translateZ(-50px) ;
}

/* 接下来我给自己准备一个编辑器 */
.resumeEditor{
  position: fixed;
  top: 50%; left: 0;
  padding: .5em;  margin: 2.5vh;
  width: 95vw; height: 45vh;
  border: 1px solid;
  background: white; color: #222;
  overflow: auto;
}
/* 好了，终于可以写简历了，您久等了 */


`,
          `
/* 这个简历好像差点什么
 * 对了，这是 Markdown 格式的，但界面看起来不是很友好
 * Emm………，可以开源工具翻译成 HTML
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

2017年毕业于江苏科技大学，大学学得软件工程，是位初级程序员，主攻Java方面，现在在寻找相关方面的工作。
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

1. APP信息管理平台，一个企业级的CMS系统，主要针对Android应用市场，开发的后台管理平台。我主要负责负责用户模块，登录模块和后台页面搭建。
2. 峰星网上购物系统，这是个电商平台，我知道您可能会感到反感，但这种项目的确有利于我对所学技术的综合应用实践。
此系统采用SSH框架，严格按照MVC设计模式，后台管理员对产品类别和产品进行管理后，前台用户通过购物车进行网上选购产品，并生成订单。
其中我主要负责购物车管理、单点登录、订单管理模块，通过这个项目我觉得我收获很多，包括对所学技术的综合应用和对一个项目完整流程有了个大体。
具体实现功能和所用技术写在静态简历里了，“我确信已发现了一种美妙的写法，可惜这里空白的地方太小，写不下。”┑(￣Д ￣)┍~

个人总结
----

- 我对自己的定位: 在深度方面主攻后端，这是立足之本，在完成本职工作之后，多学习一些高深技术，比如高并发、分布式等等，同时也要拓宽自己的广度，多了解其他方面技术。毕竟全栈才能最高效地解决问题。
- 我对工作的态度: 第一,要高效完成自己的本职工作。第二,要在完成的基础上寻找完美。第三,要在完美的基础上,与其他同事互相交流学习,互相提升。工作是一种生活方式,不仅是一份养家糊口的差事。
- 我怎样克服困难:首先我会思考困难产生的根源，尽量从源头入手，然后利用Google、Stackoverflow等等搜索寻找答案，如果已有解决方案就不再造轮子。如果没有，那就阅读源码、在手册、规范甚至REPL的环境自己做实验才是最终解决问题的办法，之后尽量总结分享出去。
- 怎样保持自己的视野:在GitHub上跟随一些大牛进行学习，订阅一些相关专栏，平时也和同学等等进行交流讨论。
- 我的优势: 热爱技术、自学能力强,有良好的自我认知，善于总结和反思。开阔的视野,良好的心态、情商与沟通能力。
- 我的劣势: 有时对待一件事情会过于自信，但当自己真正上手去做的时候才会发现其实并没有想象中那么容易，加上有时会过于追求完美，会导致事情拖延。

链接
----

* [GitHub](https://github.com/linut)
* [我的博客](https://blog.csdn.net/fenzang)

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
          this.$nextTick(() => {
            this.$refs.resumeEditor.goTop()
          })
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
    min-height: 100vh; position: relative;
  }

  html {
    min-height: 100vh;
  }
  *{
    box-sizing: border-box;
  }

</style>
