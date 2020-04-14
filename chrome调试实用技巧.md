> 工欲善其事，必先利其器

### 1. 把源码文件加到Chrome的workspace中
* 代码执行的位置也是最容易编辑代码的位置。如果你把项目的文件夹直接拖到 Source 面板，DevTools 会将你做出的修改同步到系统的文件中。

### 2. 查看chrome command，命令可以做很多事情的，比如dark theme
* 在 Chrome 的调试打开的情况下 按下 [⌘] + [Shift]+ [p]
* 或者使用 DevTools 的 dropdown 按钮下的选项 “Run command”

### 3. copying&saving
* `$_`表示上一次在console打印的内容，`$0`表示上一次选中的dom节点，`$1`表示上上次选中的dom节点，`$2`表示 ...，copy($_)，copy($0)，copy(全局变量名称)
* 如果想要对console打印出的某数据做额外的处理，但是不想要影响原来的值，那么可以选中该数据，右键并选择 “Store as global variable” (保存为全局变量) 选项，它会创建一个名为 temp1 的变量，第二次创建 temp2，第三次 ... 
* 异常报错的堆栈信息可以保存为文件
* copy html除了右键选择“copy”选项之外，还可以 [⌘] + [c]

### 4. css调整像素
* option + 箭头 ：步长为0.1
* 箭头 ：步长为1
* shift + 箭头 ：步长为10
* cmd + 箭头 ：步长为100

### 5. 快捷键
* [⌘] + [Shift]+ [d]切换控制台下或者右的位置，左或者下的位置【初始化为左】
* 按下 ctrl + [ 和 ctrl + ] 可以从当前面板的分别向左和向右切换面板。按下 ctrl + 1 到 ctrl + 9 可以直接转到编号1...9的面板(ctrl + 1转到元素面板，ctrl + 4` 转到 网络信息面板等等)，前提是setting中勾选enable ⌘ + 1-9 shortcut switch panels
* [⌘] + [k] 清除console
* [⌘] + [p] 快速查询文件
* [esc] 来显示Drawer，再次按 [esc] 隐藏

### 6. breakpoints
* 切换到source模块，选中Page，打开对应的文件，选中某行则可以在该行打上断点，你是不是也是做到这一步为止。但是，并没有结束，右键断点，选择“Edit breakpoint”，然后就可以在此处写表达式了，表达式在该断点所在之前执行，比如console.log某个变量，这样可以在源码中少写很多很多的console.log，而且也不涉及到编译热更新，配合Drawer的console面板一起使用，简直不要太爽。

### 7. monitorEvents 监控事件
* 在element模块选中一个触发元素(假如该元素的触发事件是click)，前面有说选中的这个就是`$0`，然后`monitorEvents($0,"click")`，用以检测事件是否触发

### 8. console，妙哉妙哉
* console.assert
```javascript
// 假如我们有一段console如下
let value = null;
if(!value){
  console.log("empty");
}

// 可以转换成下面这样，当我们传入的第一个参数为false时，console.assert打印该参数后面的值
console.assert(value,"empty");
```
* console.table支持对象，数组，如果你觉得展示的列太多了，使用第二个参数，传入你想要展示的列的名字，还支持排序哦
* console.dir，当我们想要打印一个dom节点时，console.log打印出来的就是元素标签，console.dir打印出来的是节点，可以查看该节点的属性
* console.time，console.timeEnd，成对存在的计时器，比较所用时间可用。如果你想一次记录多件事，可以往这些函数中传入不同的标签值（console.time('loading') ，console.timeEnd('loading')），打印出来的内容会带上标签值前缀（loading:时间间隔）
* console.log("%c我是红色的日志","color:#f00")，使用`%c`开头，第二个参数则表示样式
* 关于回调，如果只是打印回调结果，那么直接把console.log当作回调函数更加省事，`new Promise().then(console.log)`，而且你可能发现这样的方式往回调里面传的参数不止一个
* console.debug打印的内容，开启Default levels的verbose（冗长的）即可显示，console.info在其他浏览器有图标，chrome没有，console.error，console.warn有图标

### 9. network
* Request initiator显示了堆栈信息，可以从中找到调用的文件，方便debug
* 表头右键可自定义显示列
* Type为xhr的请求，右键该请求Replay XHR 可以不用刷新界面重新请求，fatch类型的不支持该操作

### 10. 其他
* 选中元素，按`h`即隐藏，再按则显示
* 右击节点后的 expand recursively命令可以展开该节点下的所有子节点，不用一个一个点击 `▶`展开
* 想要添加阴影，颜色等样式时，在某class的右下角更多选项，悬浮即可添加
* **文本**的color属性弹出的颜色选择器可查看Contrast ratio(对比度)
  * 在数字边上的 “🚫” 意味着对比度太低了
  * 一个 “✅” 意味着这个颜色遵从 [Web Content Accessibility Guidelines (WCAG) 2.0](https://www.w3.org/TR/UNDERSTANDING-WCAG20/conformance.html) 的 AA 声明，这意味着对比值至少为 4.5
  * “✅ ✅” 意味着满足了 AAA 声明，对比值至少满足7
