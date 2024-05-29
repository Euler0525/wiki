# Markdown

## Hexo

### Tabs 组件

```markdown
{% [tabs Unique name], [index] %}
<!-- tab [Tab caption] [@icon] -->
Any content (support inline tags too).
<!-- endtab -->
{% endtabs %}
```

示例

```markdown
{% tabs test, 1 %}
<!-- tab -->
This is Tab 1.
<!-- endtab -->

<!-- tab -->
This is Tab 2.
<!-- endtab -->

<!-- tab -->
This is Tab 3.
<!-- endtab -->
{% endtabs %}
```

### Note 引导标注

#### 内置标识

> 首先修改主题配置文件，启用 `icon`
>
> ```ini
> # Note (Bootstrap Callout)
> note:
>   # Note tag style values:
>   #      - simple    bs-callout old alert style. Default.
>   #      - modern    bs-callout new (v2-v3) alert style.
>   #      - flat      flat callout style with background, like on Mozilla or StackOverflow.
>   #      - disabled  disable all CSS styles import of note tag.
>   style: flat
>   icons: true
>   border_radius: 3
>   # Offset lighter of background in % for modern and flat styles (modern: -12 | 12; flat: -18 | 6).
>   # Offset also applied to label tag variables. This option can work with disabled note tag.
>   light_bg_offset: 0
> ```

```markdown
{% note [class] [icon] [style] %}
Any content (support inline tags too.io).
{% endnote %}
```

- `[class]` 表示当前类型
    - `default`, `primary`, `success`, `info`, `warning`, `danger`
- `[no-icon]`：是否显示 icon，可以替代配置中默认设置 `icon`
    - `true, false`
- `[style]` 显示的风格，可以替代配置中默认设置 `style`
    - `simple, modern, flat`

示例

```markdown
{% note flat %}
默认 提示块标签
{% endnote %}

{% note default flat %}
default 提示块标签
{% endnote %}

{% note primary flat %}
primary 提示块标签
{% endnote %}

{% note success flat %}
success 提示块标签
{% endnote %}

{% note info flat %}
info 提示块标签
{% endnote %}

{% note warning flat %}
warning 提示块标签
{% endnote %}

{% note danger flat %}
danger 提示块标签
{% endnote %}
```

#### 自定义标识

```markdown
{% note [color] [icon] [style] %}
Any content (support inline tags too.io).
{% endnote %}
```

- `[color]`: Note 标签显示的颜色
    - `default / blue / pink / red / purple / orange / green`
- `[icon]`: 可配置自定义 `icon` (也可以配置 `no-icon` )
    - 只支持 [FontAwesome 图标](https://fontawesome.com.cn/v5)
- `[style]`: 风格
    - `simple, modern, flat`

示例

```markdown
{% note 'fab fa-cc-visa' simple %}
你是刷 Visa 还是 UnionPay
{% endnote %}
{% note blue 'fas fa-bullhorn' simple %}
2021年快到了....
{% endnote %}
{% note pink 'fas fa-car-crash' simple %}
小心开车 安全至上
{% endnote %}
{% note red 'fas fa-fan' simple%}
这是三片呢？还是四片？
{% endnote %}
{% note orange 'fas fa-battery-half' simple %}
你是刷 Visa 还是 UnionPay
{% endnote %}
{% note purple 'far fa-hand-scissors' simple %}
剪刀石头布
{% endnote %}
{% note green 'fab fa-internet-explorer' simple %}
前端最讨厌的浏览器
{% endnote %}
```

### 按钮

```markdown
{% btn [url],[text],[icon],[color] [style] [layout] [position] [size] %}
```

- `[url]`: 【必须】链接
- `[text]`: 【必须】按钮文字
- `[icon]`: 图标
- `[color]`:
    - 按钮背景顔色（默认 `style` 时）
    - 按钮字体和边框颜色（`outline` 时）
    - `default/blue/pink/red/purple/orange/green`
- `[style]`: 按钮样式 默认实心
    - `outline/留空`
- `[layout]`: 按钮布局 默认为 `line`
    - `block/留空line`
- `[position]`: 按钮位置 前提是设置了 `layout` 为 `block` 默认为左边
    - `center/right/留空left`
- `[size]`: 按钮大小
    - `larger/留空`

示例

```markdown
{% btn 'https://butterfly.js.org/',Butterfly %}
{% btn 'https://butterfly.js.org/',Butterfly,far fa-hand-point-right %}
{% btn 'https://butterfly.js.org/',Butterfly,,outline %}
{% btn 'https://butterfly.js.org/',Butterfly,far fa-hand-point-right,outline %}
{% btn 'https://butterfly.js.org/',Butterfly,far fa-hand-point-right,larger %}
{% btn 'https://butterfly.js.org/',Butterfly,far fa-hand-point-right,block %}
{% btn 'https://butterfly.js.org/',Butterfly,far fa-hand-point-right,block center larger %}
{% btn 'https://butterfly.js.org/',Butterfly,far fa-hand-point-right,block right outline larger %}
```

```html
<div class="btn-center">
{% btn 'https://butterfly.js.org/',Butterfly,far fa-hand-point-right,outline larger %}
{% btn 'https://butterfly.js.org/',Butterfly,far fa-hand-point-right,outline blue larger %}
{% btn 'https://butterfly.js.org/',Butterfly,far fa-hand-point-right,outline pink larger %}
{% btn 'https://butterfly.js.org/',Butterfly,far fa-hand-point-right,outline red larger %}
{% btn 'https://butterfly.js.org/',Butterfly,far fa-hand-point-right,outline purple larger %}
{% btn 'https://butterfly.js.org/',Butterfly,far fa-hand-point-right,outline orange larger %}
{% btn 'https://butterfly.js.org/',Butterfly,far fa-hand-point-right,outline green larger %}
</div>
```

### 时间轴

```markdown
{% timeline [title],[color] %}
<!-- timeline [title] -->
xxxxx
<!-- endtimeline -->
<!-- timeline [title] -->
xxxxx
<!-- endtimeline -->
{% endtimeline %}
```

- `[color]`：`default / blue / pink / red / purple / orange / green`

示例

```markdown
{% timeline 2026,pink %}
<!-- timeline 02-27 -->
测试页面
<!-- endtimeline -->
{% endtimeline %}
```

## 参考资料

[hexo 博客 Butterfly 主题之标签外挂](https://www.catsky.org/2024/04/16/hexo%E5%8D%9A%E5%AE%A2Butterfly%E4%B8%BB%E9%A2%98%E4%B9%8B%E6%A0%87%E7%AD%BE%E5%A4%96%E6%8C%82/)
