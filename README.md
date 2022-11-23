# AndroLua_pro

原文: https://github.com/nirenr/AndroLua_pro/blob/master/README.md

lua 5.3.3 for android pro

## 关于

AndroLua 是基于 LuaJava 开发的安卓平台轻量级脚本编程语言工具，既具有 Lua 简洁优雅的特质，又支持绝大部分安卓 API，可以使你在手机上快速编写小型应用。

- [官方QQ群 236938279](http://jq.qq.com/?_wv=1027&k=dcofRr)
- [百度贴吧](http://c.tieba.baidu.com/mo/m?kw=androlua)
- [项目地址](http://sf.net/p/androlua)
- 点击[链接](https://qr.alipay.com/apt7ujjb4jngmu3z9a)支持我的工作，一块也可以哦

本程序使用了以下开源项目部分代码

- [bson,crypt,md5](https://github.com/cloudwu/skynet)
- [cjson](https://sourceforge.net/projects/cjson/)
- [zlib](https://github.com/brimworks/lua-zlib)
- [xml](https://github.com/chukong/quick-cocos2d-x)
- [luvit/luv](https://github.com/luvit/luv)
- [clibs/uv](https://github.com/clibs/uv)
- [lua-zip](https://github.com/brimworks/lua-zip)
- [libzip-android](https://github.com/julienr/libzip-android)
- [luagl](http://luagl.sourceforge.net/)
- [luasocket](https://github.com/diegonehab/luasocket)
- [sensor](https://github.com/ddlee/AndroidLuaActivity)
- canvas: 由落叶似秋开发
- jni: 由 nirenr 开发

### 与标准 Lua 5.3.1 的不同

- 打开了部分兼容选项，module，unpack，bit32
- 添加 `string.gfind` 函数，用于递归返回匹配位置
- 增加 `tointeger` 函数，强制将数值转为整数
- 修改 `tonumber` 支持转换 Java 对象

## quick start

### 1. 参考链接

关于 lua 的语法和 Android API 请参考以下网页。
- [Lua 官网](http://www.lua.org)
- [Android 中文API](http://android.toolib.net/reference/packages.html)

### 2. 导入模块

在每个脚本程序的开头应该写上 `require "import"` 以导入 `import` 模块，简化写代码的难度。目前程序内置 `bson`, `canvas`, `cjson`, `crypt`, `ftp`, `gl`, `http`, `import`, `md5`, `smtp`, `socket`, `sensor`, `xml`, `zip`, `zlib`.

### 3. 导入包或类

可以导入包或者类
```lua
import "android.widget.*"
import "android.widget.Button"
```
导入内部类
```lua
import "android.view.View_*"
```
或
```lua
import "android.view.View_OnClickListener"
```
或
```lua
View.OnClickListenr
```
包名和类名必须用引号包围。

### 4. 创建布局与组件

```lua
layout = LinearLayout(activity)
activity.setContentView(layout)
button = Button(activity)
layout.addView(button)
```
注.activity是当前窗口的Context对象，如果习惯写this只需要
```lua
this = activity
button = Button(this)
```

### 5. getter/setter

```lua
button.setText("按钮")
```
Java 的 getxxx 方法没有参数与 setxxx 方法只有一个参数时可以简写，
```lua
button.Text = "按钮"
x = button.X
```

### 6. 使用事件

创建事件处理函数
```lua
function click(s)
  print("点击")
end
```
把函数添加到事件接口
```lua
listener = View.OnClickListener{onClick = click}
```
把接口注册到组件
```lua
button.setOnClickListener(listener)
```
也可以使用匿名类
```lua
button.setOnClickListener(View.OnClickListener {
  onClick = function(s)
    print("点击")
  end
})
```

onxxx事件可以简写
```lua
button.onClick = function(v)
  print(v)
end
```

### 7. 回调方法
```lua
function onResume()
  print("返回程序")
end

function onDestroy()
  print("程序已退出")
end

function onCreateOptionsMenu(menu)
  menu.add("菜单")
end
```
支持 `onCreate`, `onStart`, `onResume`, `onPause`, `onStop`, `onDestroy`, `onActivityResult`, `onCreateOptionsMenu`, `onCreateContextMenu`, `onMenuItemSelected`

### 8. 按键与触控

```lua
function onKeyDown(code, event)
  print(code event)
end

function onTouchEvent(event)
  print(event)
end
```
支持 `onKeyDown`, `onKeyUp`, `onKeyLongPress`, `onTouchEvent`. 函数必须返布尔值

### 9. 使用数组
```lua
array = float { 1, 2, 3 }
```
或者
```lua
array = int[10]
a = array[0]
array[0] = 4
```

### 10. 使用线程

需导入 import 模块，参看 thread, timer 与 task 函数说明

#### 任务

```lua
task(str, args, callback)
```
- `str`: 任务执行代码
- `args`: 参数
- `callback`: 回调函数，任务返回值将传递到回调方法

#### 线程

```lua
t = thread(str, args)
```
- `str`: 线程中执行的代码
- `args`: 初始传入参数

#### 调用线程中方法

```lua
call(t, fn, args)
```
- `t`: 线程
- `fn`: 方法名称
- `args`: 参数

#### 设置线程变量

```lua
set(t, fn, arg)
```
- `t`: 线程
- `fn`: 变量名称
- `arg`: 变量值

#### 线程调用主线程中方法

```lua
call(fn, args)
```
- `fn`: 方法名称
- `args`: 参数

#### 线程设置主线程变量

```lua
set(fn, arg)
```
- `fn`: 变量名称
- `arg`: 变量值

> 注: 参数类型为字符串、数值、Java 对象、布尔值或 nil。
> 要使用 quit 结束线程。

#### 定时器

```lua
t = timer(func, delay, period, args)
```
- `func`: 定时器执行的函数
- `delay`: 定时器延时
- `period`: 定时器间隔
- `args`: 初始化参数

```lua
t.Enable = false -- 暂停定时器
t.Enable = true -- 启动定时器
t.stop() -- 停止定时器
```

> 注意：定时器函数定义 run 函数时，定时器重复执行 run 函数，否则重复执行构建时的 func 函数

### 11. 使用布局表

使用布局表须导入 `android.view` 与 `android.widget` 包。
```lua
require "import"
import "android.widget.*"
import "android.view.*"
```
布局表格式
```lua
layout = {
  控件名称,
  id=控件名称,
  属性=值,
  {
    子控件名称,
    id=控件名称,
    属性=值,
  }
}
```

布局表支持大部分安卓控件属性，与安卓 XML 布局文件的不同点：
- id 表示在 Lua 中变量的名称，而不是安卓的可以 findbyid 的数字 id；
- ImageView 的 src 属性是当前目录图片名称或绝对文件路径图片或网络上的图片；
- layout_width 与 layout_height 的值支持 fill 与 wrap 简写；
- onClick 值为 lua 函数或java onClick 接口或他们的全局变量名称；
- 背景 background 支持背景图片，背景色与 LuaDrawable 自绘制背景，背景图片参数为当前目录图片名称或绝对文件路径图片或网络上的图片，颜色同 backgroundColor，自绘制背景参数为绘制函数或绘制函数的全局变量名称；

绘制函数形式
```lua
function draw(canvas, paint)
  canvas.drawRect(1, 1, 100, 100, paint)
end
```
控件背景色使用 `backgroundColor` 设置，值为"十六进制颜色值"。
其他参考 `loadlayout` 与 `loadbitmap`

### 12. 2D 绘图

```lua
require "import"
import "android.app.*"
import "android.os.*"
import "android.widget.*"
import "android.view.*"
import "android.graphics.*"
activity.setTitle('AndroLua')

paint = Paint()
paint.setARGB(100, 0, 250, 0)
paint.setStrokeWidth(20)
paint.setTextSize(28)

surface = SurfaceView(activity)
callback = SurfaceHolder_Callback {
  surfaceChanged = function(holder, format, width, height)
  end,

  surfaceCreated = function(holder)
    ca = holder.lockCanvas()
    if (ca ~= nil) then
      ca.drawRGB(0, 79, 90)
      ca.drawRect(0, 0, 200, 300, paint)
    end
    holder.unlockCanvasAndPost(ca)
  end,

  surfaceDestroyed = function(holder)
  end
}

holder = surface.getHolder()
holder.addCallback(callback)
activity.setContentView(surface)
```

### 13. Lua 类型与 Java 类型

在大多数情况下 AndroLua 可以很好的处理 Lua 与 Java 类型之间的自动转换，但是 Java 的数值类型有多种 (double, float, long, int, short, byte)，而 Lua 只有 number，在必要的情况下可以使用类型的强制转换。
```lua
i = int(10) -- i 就是一个 Java 的 int 类型数据
d = double(10) -- d 是一个 Java 的 double 类型
```
在调用 Java 方法时 AndroLua 可以自动将 Lua 的 table 转换成 Java 的 array，Map 或 interface。Map 类型可以像使用 Lua 表一样简便。
```lua
map = HashMap { a=1, b=2 }
print(map.a)
map.a = 3
```
取长度运算符 `#` 可以获取 Java 中 array, List, Map, Set, String 的长度。

### 14. 部分模块

#### canvas 模块

```lua
require "import"
import "canvas"
import "android.app.*"
import "android.os.*"
import "android.widget.*"
import "android.view.*"
import "android.graphics.*"
activity.setTitle('AndroLua')

paint = Paint()
paint.setARGB(100, 0, 250, 0)
paint.setStrokeWidth(20)
paint.setTextSize(28)

surface = SurfaceView(activity)
callback = SurfaceHolder_Callback {
  surfaceChanged = function(holder, format, width, height)
  end,

  surfaceCreated = function(holder)
    ca = canvas.lockCanvas(holder)
    if (ca ~= nil) then
      ca:drawRGB(0, 79, 90)
      ca:drawRect(0, 0, 200, 300, paint)
    end
    canvas.unlockCanvasAndPost(holder, ca)
  end,

  surfaceDestroyed = function(holder)
  end
}
holder = surface.getHolder()
holder.addCallback(callback)
activity.setContentView(surface)
```

#### OpenGL 模块

```lua
require "import"
import "gl"
import "android.app.*"
import "android.os.*"
import "android.widget.*"
import "android.view.*"
import "android.opengl.*"
activity.setTitle('AndroLua')
--activity.setTheme( android.R.style.Theme_Holo_Light_NoActionBar_Fullscreen)

mTriangleData = {
  0.0, 0.6, 0.0,
  -0.6, 0.0, 0.0,
  0.6, 0.0, 0.0,
}

mTriangleColor = {
  1, 0, 0, 0,
  0, 1, 0, 0,
  0, 0, 1, 0,
}

sr = GLSurfaceView.Renderer {
  onSurfaceCreated = function(gl2, config)
    gl.glDisable(gl.GL_DITHER)
    gl.glHint(gl.GL_PERSPECTIVE_CORRECTION_HINT, gl.GL_FASTEST)
    gl.glClearColor(0, 0, 0, 0)
    gl.glShadeModel(gl.GL_SMOOTH)
    gl.glClearDepth(1.0)
    gl.glEnable(gl.GL_DEPTH_TEST)
    gl.glDepthFunc(gl.GL_LEQUAL)
  end,

  onDrawFrame = function(gl2, config)
    gl.glClear(gl.GL_COLOR_BUFFER_BIT | gl.GL_DEPTH_BUFFER_BIT)
    gl.glMatrixMode(gl.GL_MODELVIEW)
    gl.glLoadIdentity()
    gl.glRotate(0, 1, 1, 1)
    gl.glTranslate(0, 0, 0)
    gl.glEnableClientState(gl.GL_VERTEX_ARRAY)
    gl.glEnableClientState(gl.GL_COLOR_ARRAY)
    gl.glVertexPointer(mTriangleData, 3)
    gl.glColorPointer(mTriangleColor, 4)
    gl.glDrawArrays(gl.GL_TRIANGLE_STRIP, 0, 3)
    gl.glFinish()
    gl.glDisableClientState(gl.GL_VERTEX_ARRAY)
    gl.glDisableClientState(gl.GL_COLOR_ARRAY)
  end,

  onSurfaceChanged = function (gl2, w, h)
    gl.glViewport(0, 0, w, h)
    gl.glLoadIdentity()
    ratio = w / h
    gl.glFrustum(-rautio, ratio, -1, 1, 1, 10)
  end
}

glSurefaceView = GLSurfaceView(activity)
glSurefaceView.setRenderer(sr)
activity.setContentView(glSurefaceView)
```

#### http 模块

```lua
body, cookie, code, headers = http.get(url [,cookie])
body, cookie, code, headers = http.post(url ,postdata [,cookie])
code, headers = http.download(url [,cookie])
body, cookie, code, headers = http.upload(url ,datas ,files [,cookie])
```

```lua
require "import"
import "http"

-- get 函数以 get 请求获取网页，参数为请求的网址与 cookie
body, cookie, code, headers = http.get("http://www.androlua.com")

-- post 函数以 post 请求获取网页，通常用于提交表单，参数为请求的网址，要发送的内容与 cookie
body, cookie, code, headers = http.post("http://androlua.com/Login.Asp?Login=Login&Url=http://androlua.com/bbs/index.asp", "name=用户名&pass=密码&ki=1")

-- download 函数和 get 函数类似，用于下载文件，参数为请求的网址，保存文件的路径与 cookie
http.download("http://androlua.com", "/sdcard/a.txt")

-- upload 用于上传文件，参数是请求的网址，请求内容字符串部分，格式为以 key=value 形式的表，请求文件部分，格式为 key=文件路径 的表，最后一个参数为 cookie
http.upload("http://androlua.com",
  {
    title="标题",
    msg="内容"
  },
  {
    file1="/sdcard/1.txt",
    file2="/sdcard/2.txt"
  }
)
```

#### import 模块

```
require "import"
import "android.widget.*"
import "android.view.*"
layout = {
  LinearLayout,
  orientation="vertical",
  {
    EditText,
    id="edit",
    layout_width="fill"
  },
  {
    Button,
    text="按钮",
    layout_width="fill",
    onClick="click"
  }
}

function click()
  Toast.makeText(activity, edit.getText().toString(), Toast.LENGTH_SHORT).show()
end

activity.setContentView(loadlayout(layout))
```

### 关于打包

新建工程或在脚本目录新建 `init.lua` 文件。
写入以下内容，即可将文件夹下所有 lua 文件打包，`main.lua` 为程序入口。
```
appname="demo"
appver="1.0"
packagename="com.androlua.demo"
```
目录下 `icon.png` 替换图标，`welcome.png` 替换启动图。
没有 `init.lua` 文件打包当前文件。打包使用 debug 签名。

## 部分函数参考

`[a]` 表示参数 a 可选，`(...)` 表示不定参数。函数只有一个参数，且参数为字符串或表时，调用时可以省略括号。

AndroLua 库函数在 import 模块，为便于使用都是全局变量。

`s` 表示 string 类型，`i` 表示整数类型，`n` 表示浮点数或整数类型，`t` 表示表类型，`b` 表示布尔类型，`o` 表示 Java 对象类型，`f` 为 Lua 函数。`--` 表示注释。

`each(o)`
- 参数：o 实现 Iterable 接口的 Java 对象
- 返回：用于 Lua 迭代的闭包
- 作用：Java 集合迭代器

`enum(o)`
- 参数：o 实现 Enumeration 接口的 Java 对象
- 返回：用于 Lua 迭代的闭包
- 作用：Java 集合迭代器

`import(s)`
- 参数：s 要载入的包或类的名称
- 返回：载入的类或模块
- 作用：载入包或类或 Lua 模块

```lua
import "http" -- 载入 http 模块
import "android.widget.*" -- 载入 android.widget 包
import "android.widget.Button" -- 载入 android.widget.Button 类
import "android.view.View$OnClickListener" -- 载入 android.view.View.OnClickListener 内部类
```

`loadlayout(t [,t2])`
- 参数：t 要载入的布局表，t2 保存 view 的表
- 返回：布局最外层 view
- 作用：载入布局表，生成 view

```lua
layout = {
  LinearLayout,
  layout_width="fill",
  {
    TextView,
    text="Androlua",
    id="tv"
  }
}
main = {}
activity.setContentView(loadlayout(layout, main))
print(main.tv.getText())
```

`loadbitmap(s)`
- 参数：s 要载入图片的地址，支持相对地址，绝对地址与网址
- 返回：bitmap对象
- 作用：载入图片
- 注意：载入网络图片需要在线程中进行

`task(s [,...], f)`
- 参数：s 任务中运行的代码或函数，... 任务传入参数，f 回调函数
- 返回：无返回值
- 作用：在异步线程运行 Lua 代码，执行完毕在主线程调用回调函数
- 注意：参数类型包括 布尔，数值，字符串，Java 对象，不允许 Lua 对象

```lua
function func(a, b)
  require "import"
  print(a, b)
  return a + b
end
task(func, 1, 2, print)
```

`thread(s [,...])`
- 参数：s 线程中运行的 lua 代码或脚本的相对路径 (不加扩展名) 或函数，... 线程初始化参数
- 返回：返回线程对象
- 作用：开启一个线程运行 Lua 代码
- 注意：线程需要调用 quit 方法结束线程

```lua
func = [[
  a, b = ...
  function add ()
    call("print", a + b)
  end
]]
t = thread(func, 1, 2)
t.add()
```

`timer(s, i1, i2 [,...])`
- 参数：s 定时器运行的代码或函数，i1 前延时，i2 定时器间隔，... 定时器初始化参数
- 返回：定时器对象
- 作用：创建定时器重复执行函数

```lua
function f(a)
  function run()
    print(a)
    a = a + 1
  end
end
```

```lua
t = timer(f, 0, 1000, 1)
t.Enabled = false -- 暂停定时器
t.Enabled = true - -重新定时器
t.stop() -- 停止定时器
```

`new_env()`
- 参数：无
- 返回：一个继承了import模块函数的环境表
- 作用：产生一个继承import模块函数的环境表

```lua
function foo()
  local _ENV = new_env()
  import "android.widget.*"
  b = Button(activity)
end
```

`luajava.bindClass(s)`
- 参数：s class 的完整名称，支持基本类型
- 返回：Java class 对象
- 作用：载入 Java class

```lua
Button = luajava.bindClass("android.widget.Button")
int = luajava.bindClass("int")
```

`luajava.createProxy(s, t)`
- 参数：s 接口的完整名称，t 接口函数表
- 返回：Java 接口对象
- 作用：创建 Java 接口

```
onclick = luajava.createProxy("android.view.View$OnClickListener", {
  onClick = function (v)
    print(v)
  end
})
```

`luajava.createArray(s, t)`
- 参数：s 类的完整名称，支持基本类型，t 要转化为 Java 数组的表
- 返回：创建的 Java 数组对象
- 作用：创建 Java 数组

```lua
arr = luajava.createArray("int", { 1, 2, 3, 4 })
```

`luajava.newInstance(s [,...])`
- 参数：s 类的完整名称，... 构建方法的参数
- 作用：创建 Java 类的实例

```lua
b = luajava.newInstance("android.widget.Button", activity)
```

`luajava.new(o [,...])`
- 参数：o Java 类对象，... 参数
- 返回：类的实例或数组对象或接口对象
- 作用：创建一个类实例或数组对象或接口对象
- 注意：当只有一个参数且为表类型时，如果类对象为 interface 创建接口，为 class 创建数组，参数为其他情况创建实例

```lua
-- 示例中假设已载入相关类
b = luajava.new(Button,activity)
onclick = luajava.new(OnClickListener, {
  onClick = function (v)
    print(v)
  end
})
arr = luajava.new(int, { 1, 2, 3 })
```

`luajava.coding(s [,s2 [, s3]])`
- 参数：s 要转换编码的 Lua 字符串，s2 字符串的原始编码，s3 字符串的目标编码
- 返回：转码后的 Lua 字符串
- 作用：转换字符串编码
- 注意：默认进行 GBK 转 UTF8

`luajava.clear(o)`
- 参数：o Java 对象
- 返回：无
- 作用：销毁 Java 对象
- 注意：尽量避免使用此函数，除非确认不在使用此对象，且该对象比较大

`luajava.astable(o)`
- 参数：o Java 对象
- 返回：Lua 表
- 作用：转换 Java 的 Array List 或 Map 为 Lua 表

`luajava.tostring(o)`
- 参数：o Java 对象
- 返回：Lua 字符串
- 作用：相当于 o.toString()

### activity 部分 API 参考

- `setContentView(layout, env)`

  设置布局表 layout 为当前 activity 的主视图，env 是保存视图 ID 的表，默认是 _G

- `getLuaDir()`

  返回脚本当前目录

- `getLuaDir(name)`

  返回脚本当前目录的子目录

- `getLuaExtDir()`

  返回 AndroLua 在 SDCard 的工作目录

- `getLuaExtDir(name)`

  返回 AndroLua 在 SDCard 的工作目录的子目录

- `getWidth()`

  返回屏幕宽度

- `getHeight()`

  返回屏幕高度，不包括状态栏与导航栏

- `loadDex(path)`

  加载当前目录 dex 或 jar，返回 DexClassLoader

- `loadLib(path)`

  加载当前目录 c 模块，返回载入后模块的返回值(通常是包含模块函数的包)

- `registerReceiver(filter)`

  注册一个广播接收者，当再次调用该方法时将移除上次注册的过滤器

- `newActivity(req, path, arg)`

  打开一个新 activity，运行路径为 path 的 Lua 文件，其他两个参数为可选，arg 为表，接受脚本为变长参数

- `newTask(func, update, callback)`

  新建一个 Task 异步任务，在线程中执行 func 函数，其他两个参数可选，执行结束回调 callback，在任务调用 update 函数时在 UI 线程回调该函数

  新建的 Task 在调用 execute{} 时通过表传入参数，在 func 以 unpack 形式接收，执行 func 可以返回多个值，

- `newThread(func, arg)`

  新建一个线程，在线程中运行 func 函数，可以以表的形式传入 arg，在 func 以 unpack 形式接收

  新建的线程调用 start() 方法运行，线程为含有 loop 线程，在当前 activity 结束后自动结束 loop

- `newTimer(func, arg)`

  新建一个定时器，在线程中运行 func 函数，可以以表的形式传入 arg，在 func 以 unpack 形式接收

  调用定时器的 start(delay, period) 开始定时器，stop() 停止定时器，Enabled 暂停恢复定时器，Period 属性改变定时器间隔
