UML 文件是 UI 描述文件，可以用来创建于代码解耦的可定制界面。

UML 文件格式是纯文本的人类可读格式。UML 由 Element（元素）和变量定义语句构成，每个元素占据一或多行。

# 如何调试

在 `phira/src/scene/event.rs` 中将 `DEBUG_MODE` 设置为 `true`，然后在当前目录（你运行 Phira 客户端的所在目录）下新建名为 `test.uml` 的文件。此后任意进入一个 event，该 event 的界面将读取自 `test.uml`，并实时更新（更新 `test.uml` 后界面也会自动刷新）。

# 坐标系统

屏幕宽度总是为 `2`，高度为 `实际高度 / 实际宽度 * 2`。按照此比例对长度进行换算。

# 数据类型

- `Float`（`f32`）：单精度浮点数；
- `Rect`：由四个 `Float` 组成（`[x, y, w, h]` 分别为左上角 `x` 坐标、左上角 `y` 坐标和宽、高）。可以通过 `r.x`、`r.y`、`r.w`、`r.h` 来获取矩形的位置大小，也可以访问 `r.r` 和 `r.b` 来获得矩阵的右下角 `x` 坐标和 `y` 坐标；
- `Bool`：布尔值，`true` 或 `false`，只能用于属性中；
- `Color`：颜色，可以是 `red`、`blue` 等常见颜色，也可以是 `w0.3` 用来表示不透明度为 `0.3` 的白色，`b0.9` 用来表示不透明度为 `0.9` 的黑色，也可以是最常见的 `#2196f3` 这种十六进制颜色值。

# 变量定义语句

```
let name = value
```

变量可以被重复定义，以最后一次为准。定义变量后，你就可以在表达式中使用它，例如：

```
let pi = 3.14
let radius = 5
let area = radius * radius * pi
```

# 元素

元素的基本表述格式如下：

```
type(attr1: value1, attr2: value2, ...) { Hello! }
```

其中，`type` 为元素的类型，`attrN` 和 `valueN` 分别为属性名称和属性值。不同类型的元素的属性也各不相同，其中一些是必填，会在介绍元素时具体标注。

后面的 `{ Hello }` 是可选内容，代表该元素的文字内容。只对 `p`（文本元素）生效。

## 元素 ID

`id` 为所有元素共有的属性。`id` 是可选的，当 `id` 被设置后，`id` 对应的变量就会被赋值为该元素的绘制范围（类型为 `Rect`）。下面的例子演示了如何获取一个文本元素的高度：

```
p(id: r, size: 1.2) { Hello, World! }

let height = r.h
```

## 文本元素 `p`

```
p(id, x, y, ax, ay, size, ml, mw, bl, c)
```

- `id`（选填）：元素 ID；
- `x`（选填，默认为 `0`）：文本 `x` 坐标；
- `y`（选填，默认为 `0`）：文本 `y` 坐标；
- `ax`（选填，默认为 `0`）：文本横向对齐，`[0, 1]` 间的实数。为 `0` 时，按照 `x` 坐标左对齐，为 `1` 时右对齐，`0.5` 时居中；
- `ay`（选填，默认为 `0`）：文本纵向对齐，具体意义同上；
- `size`（选填，默认为 `1`）：文本大小；
- `ml`（选填，默认为 `false`）：文本是否多行渲染，只在设置了 `mw` 的条件下有效；
- `mw`（选填）：文本最大宽度。在单行模式下，超出范围的文本将被省略；多行模式下将自动换行；
- `bl`（选填，默认为 `true`）：纵向对齐时是否按照基准线对齐；
- `c`（选填，默认为白色）：文本颜色。

## 图片元素 `img`

```
img(id, url, r, c, t)
```

- `id`（选填）：元素 ID；
- `url`（必填）：图片网址；
- `r`（必填）：图片显示的位置（类型为 `Rect`）；
- `c`（选填，默认为白色）：在图片上叠加的颜色。若颜色半透明，图片也会半透明；
- `t`（选填，默认为 `cropCenter`）：图片的放缩类型，有 `cropCenter`、`inside` 和 `fit` 三种。

## 谱面合集 `col`

```
col(id, cid, r, rn, rh)
```

- `id`（选填）：元素 ID；
- `cid`（必填）：谱面合集 ID（`collection`）；
- `r`（必填）：谱面合集显示的位置（类型为 `Rect`）；
- `rn`（选填，默认为 `4`）：一行显示的谱面数目；
- `rh`（选填，默认为 `0.3`）：谱面的高度。

# 内置变量

UML 一般会内置一些变量：

- `t`：浮点数，时间（单位为秒）；
- `o`：浮点数，见于活动界面，是滑动的距离，可以用来做滑动动画；
- `top`：浮点数，`实际高度 / 实际宽度`。

# 例子

下面是一个简单的例子：

```
p(id: r, x: 1, ax: 0.5) { Hello, World! }

p(id: r, x: 1, y: r.b + 0.03, ax: 0.5, ml: true, c: blue) {
	Nice to meet you!
	How are you?
}

img(url: "https://files-cf.phira.cn/tako.jpg", r: [r.x, r.b + 0.04, r.w, 0.2])
```

![[Pasted image 20230625000454.png]]