---
title: Android 应用开发基础知识杂记
date: 2020-03-11 23:40
tag: 
    - Android
    - 应用程序
    - 基础
categories: 
    - 重学Android

---

## Android 资源分类
| 目录 | 资源类型 |
|------|----------|
| animator | 定义[属性动画](https://developer.android.com/guide/topics/graphics/prop-animation)的 XML 文件 |
| anim     | 定义 [tween 动画](https://developer.android.com/guide/topics/graphics/view-animation#tween-animation)的  XML 文件（属性动画的 XML 文件也可以放在这里，但最好把这两类动画区分开） |
| color    | 定义颜色的 XML 文件，参考 [Color State List Resource](https://developer.android.com/guide/topics/resources/color-list-resource)         |
| drawable | 位图文件(.png .9.png .jpg .gif)，编译成以下drawable 子类型的 XML 文件: Bitmap、 9-pathes、 State lists、Shapes、Animation drawables 等,参考  [Drawable Resources](https://developer.android.com/guide/topics/resources/drawable-resource)         |
|mipmap     | 不同屏幕密度的启动图标定义，参考[Managing Projects Overview](https://developer.android.com/tools/projects#mipmap) |
|layout     | 界面布局 XML 文件，参考 [Layout Resource](https://developer.android.com/guide/topics/resources/layout-resource) |
|menu       | 定义菜单的 XML 文件，例如 Option Menu、Context Menu、Sub Menu，参考 [Menu Resources](https://developer.android.com/guide/topics/resources/menu-resource) |
|raw        | 使用原始格式保存的任意文件。调用 Resources.openRawResource(R.raw.filename) 来打开此类文件的 InputStream 。如果你需要访问原始的文件和文件夹结构，那么你可以考虑把文件放到 `assets` 目录，而不是 `res/raw` 目录下。`assets` 目录下的文件是没有资源 ID 的，只能通过 [AssetManager](https://developer.android.com/reference/android/content/res/AssetManager) 来访问 |
| values    | 保存简单数据的 XML 文件。在 res 目录下的各个 XML 文件都是用唯一的 ID 来标识一个文件的，。但是这里的一个 XML 文件会包含多个资源，每个资源的标签都会定义一个资源，比如 <string> 定义一个字符串，使用 R.string.xxx 访问，<color> 标签定义个 R.color.xx。通常为了代码结构清晰，根据惯例使用以下几个 XML 文件来定义对应类型的资源（你当然可以把所有资源的定义都放在一个 XML 中）： [arrays.xml](https://developer.android.com/guide/topics/resources/more-resources#TypedArray) 、[colors.xl](https://developer.android.com/guide/topics/resources/more-resources#Color)、 [strings](https://developer.android.com/guide/topics/resources/more-resources#Dimension)、[styles.xml](https://developer.android.com/guide/topics/resources/style-resource) |
|xml        | 任意 XML 文件，通过 [Resources.getXML()](https://developer.android.com/reference/android/content/res/Resources#getXml(int)) 来访问。 |
| font      | 字体文件和包含 <font-family> 的 XML 文件，参考 [Fonts in XML](https://developer.android.com/preview/features/fonts-in-xml)|

> 关于资源的更多资料，参考 [Resource Types](https://developer.android.com/guide/topics/resources/available-resources)

## Configuration qualifiers
|Configuration      |Qualifier Values       | Description      |
|---------------|----------|----------|
|MCC & MNC      |比如：mcc310、mcc310-mnc004  | 根据[国家](https://developer.android.com/reference/android/content/res/Configuration#mcc)、[运营商](https://developer.android.com/reference/android/content/res/Configuration#mnc)选择资源 |
|语言与区域     |比如：en、zh-CN、fr-rCA     ||
