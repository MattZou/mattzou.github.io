---
layout: post
title: ArcGIS上下角标
author: MattZou
date: 2019/07/07
categories: ArcGIS
tags: 
- 角标
---

绘制专题图时，Legend或Title部分存在上下标。
<!-- more -->

### 方法
ArcGIS标注系统支持类html标签，直接在文本设置中填入对应角标即可。
![上下标](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ArcGIS/Arcgis_%E8%A7%92%E6%A0%87.jpg/pic)


### 拓展
其他受支持的标签如下
<table>
<thead>
  <tr>
    <th>元素描述</th>
    <th>起始标签</th>
    <th>结束标签</th>
    <th>有效的属性值</th>
    <th>备注</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>字体名称和/或字号</td>
    <td>&lt;FNT&gt;</td>
    <td>&lt;/FNT&gt;</td>
    <td>name = {TrueType font} size = {1} scale = {1-}</td>
    <td>设置名称、大小和比例，或全部设置。</td>
  </tr>
  <tr>
    <td>颜色(RGB)</td>
    <td>&lt;CLR&gt;</td>
    <td>&lt;/CLR&gt;</td>
    <td>red, green, blue = {0-255}</td>
    <td>缺失的颜色属性假定为 0</td>
  </tr>
  <tr>
    <td>颜色 (CMYK)</td>
    <td>&lt;CLR&gt;</td>
    <td>&lt;/CLR&gt;</td>
    <td>cyan, magenta, yellow, black = {0-100}</td>
    <td>缺失的颜色属性假定为 0</td>
  </tr>
  <tr>
    <td>粗体</td>
    <td>&lt;BOL&gt;</td>
    <td>&lt;/BOL&gt;</td>
    <td>无</td>
    <td> </td>
  </tr>
  <tr>
    <td>斜体</td>
    <td>&lt;ITA&gt;</td>
    <td>&lt;/ITA&gt;</td>
    <td>无</td>
    <td> </td>
  </tr>
  <tr>
    <td>下划线</td>
    <td>&lt;UND&gt;</td>
    <td>&lt;/UND&gt;</td>
    <td>无</td>
    <td> </td>
  </tr>
  <tr>
    <td>全部大写</td>
    <td>&lt;ACP&gt;</td>
    <td>&lt;/ACP&gt;</td>
    <td>无</td>
    <td> </td>
  </tr>
  <tr>
    <td>小型大写字母</td>
    <td>&lt;SCP&gt;</td>
    <td>&lt;/SCP&gt;</td>
    <td>无</td>
    <td> </td>
  </tr>
  <tr>
    <td>上标</td>
    <td>&lt;SUP&gt;</td>
    <td>&lt;/SUP&gt;</td>
    <td>无</td>
    <td> </td>
  </tr>
  <tr>
    <td>下标</td>
    <td>&lt;SUB&gt;</td>
    <td>&lt;/SUB&gt;</td>
    <td>无</td>
    <td> </td>
  </tr>
  <tr>
    <td>字符间距</td>
    <td>&lt;CHR&gt;</td>
    <td>&lt;/CHR&gt;</td>
    <td>spacing = {1-}</td>
    <td>表示相对于常规字符间距的调整百分比；0% 表示没有调整。</td>
  </tr>
  <tr>
    <td>字符宽度</td>
    <td>&lt;CHR&gt;</td>
    <td>&lt;/CHR&gt;</td>
    <td> </td>
    <td>表示相对于常规字符宽度的调整百分比；0% 表示没有调整。</td>
  </tr>
  <tr>
    <td>词间距</td>
    <td>&lt;WRD&gt;</td>
    <td>&lt;/WRD&gt;</td>
    <td>spacing = {1-}</td>
    <td>表示词间的间距百分比；100% 表示常规间距。</td>
  </tr>
  <tr>
    <td>行间距</td>
    <td>&lt;LIN&gt;</td>
    <td>&lt;/LIN&gt;</td>
    <td>leading = {1-}</td>
    <td>表示相对于常规行间距的调整（单位为磅）；0 磅表示没有调整。</td>
  </tr>
  <tr>
    <td>不加粗</td>
    <td>&lt;_BOL&gt;</td>
    <td>&lt;/BOL&gt;</td>
    <td>无</td>
    <td> </td>
  </tr>
  <tr>
    <td>不倾斜</td>
    <td>&lt;_ITA&gt;</td>
    <td>&lt;/_ITA&gt;</td>
    <td>无</td>
    <td> </td>
  </tr>
  <tr>
    <td>不加下划线</td>
    <td>&lt;_UND&gt;</td>
    <td>&lt;/_UND&gt;</td>
    <td>无</td>
    <td> </td>
  </tr>
  <tr>
    <td>不加上标</td>
    <td>&lt;_SUP&gt;</td>
    <td>&lt;/_SUP&gt;</td>
    <td>无</td>
    <td> </td>
  </tr>
  <tr>
    <td>不加下标</td>
    <td>&lt;_SUB&gt;</td>
    <td>&lt;/_SUB&gt;</td>
    <td>无</td>
    <td> </td>
  </tr>
</tbody>
</table>