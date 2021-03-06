<!-- 安装vue官方脚手架 -->
## vue-cli 脚手架安装

``` shell
npm install vue-cli
// options
vue
vue-list
vue init webpack#1.0 vue-ele

cd vue-app
npm install
// webstom 开启项目会检索文件node_modules文件很多，需要屏蔽 file->setting->directories->选择node_modules
 或者 删除node_modules，在项目当中创建一个空的node_modules文件夹—>右击->Mark Directory as->excluded-npm install,这样也可以忽略node_modules文件
```
## 项目目录文件介绍

- build和config是webpack的相关配置文件
- src 项目代码文件
- static 项目依赖的第三方文件
- babelrc babel编译es6到es5的配置
  - presets 预设 表示babel需要提前安装的插件
    - stage-2
  - plugins 除了预设插件以外的其他插件
    - transform-runtime 把一些es6的插件做转换
    - comments 表示代码转换后不生成注释
- .editorconfig
  - charset = utf-8
  - indent_style = space 以空格方式做缩进
  - indent_size = 2 缩进大小vue风格两个
  - end_of_line = lf linux和mac换行符风格
  - insert_final_newline = true 自动换行文件 在末尾插入一个新行
  - trim_trailing_whitespace = true
- .eslintrc.js
  - eslint语法对于符号，空格规范到变态，建议webstom写完ES6代码后，ctrl+alt+l 格式化一下代码
- .eslintignore 表示对里面的配置文件不会做语法检查
  - build/*.js 忽略对build文件下的所有文件做语法检查
  - config/*.js 忽略对config文件下面的所有文件做语法检查
  - .eslintrc.js eslint配置项
    - extends:'standard' 在项目初始化的时候让它继承标准的规则
    - 'rule' 具体定义的es6规则 不想要的规则配置成0即可
- .gitignore git push时候忽略掉的文件
- package.json 项目生产和开发环境需要的一些配置文件和命令配置
  - "script" 表示执行的命令，例如运行npm run XXX 对应执行的代码
  - "dependencies" 生产环境下需要的依赖
  - "devDependencies" 编译环境下需要的依赖

## 准备项目

### 目录（脚手架生成）
### 需求分析
### 项目resource
### 图标fonts
  - iconMoon ->iconMoonApp(可以使用它提供的也可以自己导入svg小图)
  - 左上角import Icons 选择自己的svg图片，选择想要的小图标，点击左下角generate生成图标文件
### mockData和配置api`require(../data.json)/express.Router()`

  ```javascript
  // bulid/dev-server.js
  var apiRoutes = express.Router();
  app.use('/api',apiRoutes);
  apiRoutes.get('/seller',function (req,res) {
    res.json({
      errno: 0,
      data: seller
    });
  });
  ```
## 页面布局框架搭建

### 组件拆分 `header/tab/content/ footer`

  ```javascript
  // 指令标签和HTML标签重命报错，所以注册组件的时候，需要自定义命名
  export default {
    components: {
      'v-header': header
    }
  }
  ```
  - header: retina 1px像素border实现
    - ipconfig 草料二维码 手机端二维码 同一局域网 注意浏览器内核

    ```javascript
    // base.styl
      @media (-webkit-min-device-pixel-ratio: 1.5),(min-device-pixel-ratio: 1.5)
      .class-border-1px
      &::after
        -webkit-transform: scaleY(0.7)
        -moz-transform: scaleY(0.7)
        -ms-transform: scaleY(0.7)
        -o-transform: scaleY(0.7)
        transform: scaleY(0.7)
      @media (-webkit-min-device-pixel-ratio: 2.0),(min-device-pixel-ratio: 2.0)
      .class-border-1px
      &::after
        -webkit-transform: scaleY(0.7)
        -moz-transform: scaleY(0.7)
        -ms-transform: scaleY(0.7)
        -o-transform: scaleY(0.7)
        transform: scaleY(0.7)
    ```

  - tab：flex自适应布局
    - 切换Vue-router（npm install vue-router@0.7.13）`v-link="{path:'/goods'}"`
  - content: router-view外联`<router-view></router-view>`
    - router.go('/goods') 配置页面加载默认路由

## 页面布局框架搭建

### vue-resource `ajax请求`
  - 子组件，需要渲染的数据可以有父组件一次请求拿到；也可以子组件自己拿，但是组件较多的情况下，请求数据次数变多。
  - 数组由子组件自己拿，还是父组件一次性拿，取决于组件复用性和真实的业务需求。data() {return{}}
  - this.$http中的this上下文指的就是这个.vue组件实例。this.seller 的this指的是上面定义的data(){} 方法暴露的实例{};
  - 304请求未修改，用的是缓存，可使用ctrl+f5强制刷新。
  - 0.9版本的时候，http.md文档当中用.json()方法返回的是一个object对象，可以直接渲染数据，但是1.0版本返回的是一个.json 返回的是一个promise对象，数据从body里面拿去，这个坑，我看文档查了好久。console.log（this.seller）可以查看控制台返回的数据对象

### header组件编写
  - vue-resource拿到的数据渲染，在父组件的组件定义指令当中，用v.bind='seller'或者:seller='seller'这里推荐使用缩写形式
  - 数据父组件传子组件，子组件用props:{}去接数据对象
  - 有了seller这个数据结构，就可以编写template里面的数据结构;
  - 注意溢出省略号的三个属性white-space/overflow/text-overflow
  - 本图层设置rgba(x,x,x,0.2)透明效果，子层文字颜色的透明继承。
  - 背景层filter:blur(10px)之后，会看到模糊边沿溢出，需要给父元素设置overflow:hidden;

### 实现详情弹层页面
  - 实现弹出层效果。浮层可以用fixed布局和z-index:100，但是footer的fixed固定也是相对于窗口的，有时候内容层比较长，footer层fixed布局的话会出现覆盖，可以用经典的CSS-sticky-footer
  - 实现浮层布局和背景透明等，v-show指令 来控制浮层显示和隐藏，data(){return{detailShow:false}},通过data(){}实例对象传值
  - 点击显示浮层：按钮等事件监听方法@click 通过methods:{}对象来控制，里面写方法，注意this.dataSow = true 会跟踪，data() 方法里面的变量，监听通知dom元素当中的便令发生变化。注意：方法和属性名不能同名。
  - 点击隐藏浮层
  - css-sticky-footer 注意同级元素上下margin会重叠，看谁的值比较大

    ```html
    //css-sticky(css黏性布局)
    <div v-show="detailShow" class="detail">
      <div class="detail-content-wrapper clearfix">
        <div class="detail-content"></div>
      </div>
      <div class="footer"></div>
    </div>
    ```
  - star(评论标新组件) 不同评级的星星实现组件抽象，v-for传参数实现灵活的组件运用 computed:{property} 更具不同图标大小计算属性。在模板里面绑定复杂表达式会让组件变得难以维护，所以任何复杂逻辑，都要计算属性
  - 注意v-for的track-by='$index'
  - 小标题左右会有两个长线，要做到不同屏幕宽度下面的自适应，可以用经典的flex布局。
  - flex布局，一般用的是div元素，如果用span元素，在某些安卓浏览器下面可能会用兼容性问题。这个是血一样的教训。
  - CSS3渐变很简单元素加transition=“fade” 在样式表当中定义终态fade-transition，然后定义进入的fade-enter，退出的fadeout
  - backdrop：blur（10px）只有在ios上才有效果。

### 实现商品详情页面
  - 左右两栏flex布局。每一栏flex布局的

  ```javascript
    .goods
        display: flex
        position absolute
        top 174px
        bottom: 46px
        width: 100%
        overflow: hidden
        .menu-wrapper
          flex: 0 0 80px
          //考虑到安卓端flex布局的兼容性问题，flex宽度定完之后，给个固定的宽度。
          width: 80px
          background: #f3f5f7
        .foots-wrapper
          flex: 1

  ```

  - menu 菜单
   - display: table 垂直居中最好的布局。
  - food 食品展示栏
   - 一般建议直接在img元素里面设置好width和height
  - better-scroll 插件使用
   - v-el:foot-wrapper 定位DOM（中划线-命名不能用驼峰）

    ```javascript
      <div v-el:></div>
      //js代码当中通过this.$els.XXX获取DOM元素
      this.menuScroll = new BScroll(this.$els.foodWrapper)
    ```
   - 我们知道vue是数据驱动DOM的，但是这个过程是异步的，当数据加载完成之后，可能这个DOM还没更新。在created的时候，虽然数据跟新了，但是DOM没有实时跟新更新，用better-scroll去初始化高度的时候计算就会有问题。
   - 所以我们这里要用到一个vue的接口$nextTick(),数据跟新之后是通过这个区更新DOM。在这个接口里去初始化插件方法，方法才会有效。


   
# ele-app
> sell


## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# run unit tests
npm run unit

# run e2e tests
npm run e2e

# run all tests
npm test
```

For detailed explanation on how things work, checkout the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).
