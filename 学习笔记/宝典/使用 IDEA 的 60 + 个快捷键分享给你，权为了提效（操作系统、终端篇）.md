> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [mp.weixin.qq.com](https://mp.weixin.qq.com/s/V32Xq2eijPH1V7hzdCPKKw)

> 扔掉鼠标，提高效率。本文已被 https://yourbatman.cn 收录；[女娲 Knife-Initializr 工程](https://mp.weixin.qq.com/s?__biz=MzI0MTUwOTgyOQ==&mid=2247495645&idx=1&sn=c325e82ab8bb9b98261f54a990e7b585&scene=21#wechat_redirect)可公开访问啦；程序员专用网盘 [https://wangpan.yourbatman.cn](https://mp.weixin.qq.com/s?__biz=MzI0MTUwOTgyOQ==&mid=2247495234&idx=1&sn=93957a448885baa3c254bfe0b2895a8d&scene=21#wechat_redirect)；技术专栏源代码大本营：https://github.com/yourbatman/tech-column-learning；公号后台回复 “**专栏列表**” 获取全部小而美的**原创**技术专栏

你好，我是[**方向盘 (YourBatman)**](https://mp.weixin.qq.com/s?__biz=MzI0MTUwOTgyOQ==&mid=2247483765&idx=1&sn=591d5e6503aed1af1eb5ed8a053aea7c&scene=21#wechat_redirect)。笔者的公号是保留地，**只分享原创，不转载、不发商务广告！**

✍前言
===

说到快捷键，对于程序员朋友一定都不陌生。哪些快捷键是你最常用的呢？没错，那必然是复制 + 粘贴快捷键。

大家好，我来敲代码了，ctrl+c 再 ctrl+v！![](https://mmbiz.qpic.cn/mmbiz_png/crPesQVeyKK7xt05ZCQJjsAUP8Yv9U8BnphPGDR3GHPJgtiaFwM2C8micbuBibxaSgh9Cgc2REYiccBaaFicibsZcqLw/640?wx_fmt=png)“CV 大法”固然好，但其实快捷键的道路上不仅仅只有它，还有 “诗和远方” 呢。

每个操作系统、每个 IDE、每个应用都有着非常多的快捷键（几十甚至几百个），足矣看到软件设计者们的重视吧。本文将本着分享的目的，**以 IDEA 的快捷键为着力点**分享笔者自己常用的一些快捷键，若能够帮助你解锁更多姿势岂不乐哉。

> 说明：快捷键之于每个人的喜好、偏爱不尽相同（但笔者相信绝大多数同学都是趋同的），本文仅以分享为目的，酌情取之

提示：快捷键的数量非常之庞大，每个都强行去使用可能适得其反。不用刻意为之，建议遵循**二八原则**，有的放矢。

#### 所属专栏

*   [【方向盘】-IntelliJ IDEA](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0MTUwOTgyOQ==&action=getalbum&album_id=1374601107946962947#wechat_redirect)
    

#### 相关下载

*   【本专栏源代码】：https://github.com/yourbatman/FXP-java-ee
    
*   【技术专栏源代码大本营】：https://github.com/yourbatman/tech-column-learning
    
*   【女娲 Knife-Initializr 工程】访问地址：http://152.136.106.14:8761
    
*   【程序员专用网盘】公益上线啦，注册送 1G 超小容量，帮你实践做**减法**：[https://wangpan.yourbatman.cn](https://mp.weixin.qq.com/s?__biz=MzI0MTUwOTgyOQ==&mid=2247495234&idx=1&sn=93957a448885baa3c254bfe0b2895a8d&scene=21#wechat_redirect)
    
*   【Java 开发软件包 (Mac)】：https://wangpan.yourbatman.cn/s/rEH0 提取码：javakit
    

#### 版本约定

*   Mac OS 12.1
    
*   iTerm2 3.4.14（zsh 5.8）
    
*   IntelliJ IDEA 2021.3.1
    

✍正文
===

**每个程序员都相信快捷键可以成倍提高效率，但只有少数人愿意为之**。

笔者以自己为例，从操作系统层面、终端层面、IDE 层面分享常用的快键键操作。申明：以下列出快捷键都是本人平时最常使用的，属于经验之谈而并非未经实战的文档性教程。

操作系统层面快捷键
---------

![](https://mmbiz.qpic.cn/mmbiz_png/crPesQVeyKK7xt05ZCQJjsAUP8Yv9U8BmVttaKxcmMib6NR4eibjXTd8Dpo1KjXOkGaiagnvXWLzrM5Sjq5AUFrQw/640?wx_fmt=png)以下快捷键以 Mac OS 为例。

### ✌ 通用快捷键

![](https://mmbiz.qpic.cn/mmbiz_png/crPesQVeyKK7xt05ZCQJjsAUP8Yv9U8B2XLTxbgw7OggGPjuPYsembd7eMl8myvoF2UbuftzCJ4ibe0HrfOuicsQ/640?wx_fmt=png)command 是 mac 里最重要的组合键，以简写的`cmd`代替。

这部分快捷键具有普适性：几乎在每个 App 里都有同样的行为。所以是最简单、最常用，当然也是最重要的。

<table><thead><tr><th>快捷键</th><th>功能说明</th></tr></thead><tbody><tr><td>cmd + c</td><td>复制 / 拷贝</td></tr><tr><td>cmd + v</td><td>粘贴</td></tr><tr><td>cmd + x</td><td>剪切</td></tr><tr><td>cmd + z</td><td>撤销</td></tr><tr><td><code>cmd + shift + z</code></td><td>取消撤销</td></tr><tr><td>cmd + n</td><td>新建 (文档、窗口等)</td></tr><tr><td>cmd + o</td><td>打开文件 / 文件夹</td></tr><tr><td>cmd + s</td><td>保存</td></tr><tr><td><code>cmd + shift + s</code></td><td>保存全部 (标签页)</td></tr><tr><td>cmd + a</td><td>全选</td></tr><tr><td>cmd + q</td><td>退出 (当前正在使用的 App)</td></tr><tr><td><code>cmd + w</code></td><td>关闭当前窗口 / 标签页 (App 并未退出)</td></tr><tr><td>cmd + deleted</td><td>删除文件 (夹)</td></tr><tr><td><code>cmd + t</code></td><td>打开新标签页</td></tr><tr><td>cmd + i</td><td>查看文件信息</td></tr><tr><td>cmd + f</td><td>查找</td></tr><tr><td>cmd + h</td><td>隐藏当前窗口 (全屏下无效)</td></tr><tr><td>cmd + m</td><td>最小化当前窗口 (全屏下无效)</td></tr><tr><td>cmd + r</td><td>刷新 (页面)</td></tr><tr><td>cmd + ,</td><td>打开偏好设置</td></tr><tr><td><code>cmd + 空格</code></td><td>打开聚焦搜索</td></tr></tbody></table>

### ✌ 场景快捷键

下面这些快捷键，在常见的一些场景里派上用场。

#### 拖动顶部菜单栏图标顺序

你知道吗，Mac 顶部菜单栏的图标顺序是可以拖动，自定义排序的。做法是：按住 cmd 键，使用鼠标移动![](https://mmbiz.qpic.cn/mmbiz_png/crPesQVeyKK7xt05ZCQJjsAUP8Yv9U8Bp67bUw9EPLngFnVK8bqgJAKmgD92z6gcfnKabDQmFHQOuLKvRzyKNQ/640?wx_fmt=png)

#### 预览内容

这是 Mac 非常实用的功能：不用打开文件 / 文件夹，快速预览内容。这在看图、看文件时非常好用。用法为：选中文件 / 文件夹，单击 “空格键” 即可。![](https://mmbiz.qpic.cn/mmbiz_png/crPesQVeyKK7xt05ZCQJjsAUP8Yv9U8BkDAxddhLQ3LTYVxaHQjibmg6nicZY6icKZNzQ5ia2jNSORwTsXjKtZURAQ/640?wx_fmt=png)![](https://mmbiz.qpic.cn/mmbiz_png/crPesQVeyKK7xt05ZCQJjsAUP8Yv9U8BJZWcfkCIXbtqY08151KzLN6r7Gx8HlNrdag3qsJwIWBFToeIz0CSfg/640?wx_fmt=png)

#### 强制退出应用

在 windows 时，当软件卡死点击 x 无法关闭应用时，可以调起资源管理器，来进行强行关闭。![](https://mmbiz.qpic.cn/mmbiz_png/crPesQVeyKK7xt05ZCQJjsAUP8Yv9U8B5wYK2fT0r6dUf4XUmyjvxujyhA3EX8RPW6uScpR4FpY0y6cPZ1jwNA/640?wx_fmt=png)在 Mac 中也有类似的操作方式：强制退出。快键键是：`cmd + option + esc`（这几个键左手单手很难完成，建议左手 cmd+esc + 右手 option）

> 个人习惯建议：对于 option 这个组合键，一般都使用右手来触达比较方便些

![](https://mmbiz.qpic.cn/mmbiz_png/crPesQVeyKK7xt05ZCQJjsAUP8Yv9U8BrBvfPvicibHAlmhKShQGicpGvJU6D1NXfFLfxCgEJWibicWgXaVFPOb6WDQ/640?wx_fmt=png)

#### 文件重命名

Mac 对文件的重命名非常方便，操作为：选中文件，按回车。![](https://mmbiz.qpic.cn/mmbiz_png/crPesQVeyKK7xt05ZCQJjsAUP8Yv9U8BpwFwhXbJvL994zSmyZyz0Cqa5njTTPxPjfhHHuB1DMicDVZYyDtoAOw/640?wx_fmt=png)这里可能有些同学就不习惯了：按回车一般不都是打开文件 / 文件夹吗？

是的，这确实是和 windows 有点不一样的地方。在 Mac 里打开文件 / 文件夹是使用`cmd + o`组合键来完成的。

### ✌ 神奇的 option 键

它在 Mac 里的作用挺神奇的，很多老粉称它为神奇键、魔力键。![](https://mmbiz.qpic.cn/mmbiz_png/crPesQVeyKK7xt05ZCQJjsAUP8Yv9U8BFnILjyjs1jAavPdalCibgJfmojLNBpdE1HxlN3QRv3otibtibH8HkISVA/640?wx_fmt=png)

#### 显示文件路径

和 windows 会在顶部实时显示当前路径不一样，Mac 若想查看选中文件的完整路径，可以这么操作：选中文件，按下 option 键一小会，底部状态栏就可以看到该文件的全路径啦。![](https://mmbiz.qpic.cn/mmbiz_png/crPesQVeyKK7xt05ZCQJjsAUP8Yv9U8B47svIs7gkKEmE2EB4sZt39mZWb8u2BMO2lJOytjhL76icFelb2yDicNg/640?wx_fmt=png)

#### 加强右键菜单

Mac 的右键菜单看似没有 Windows 那样来得丰富，其实不然。比如：这是正常的右键菜单![](https://mmbiz.qpic.cn/mmbiz_png/crPesQVeyKK7xt05ZCQJjsAUP8Yv9U8BghianZFS61uQeGTf87sdxEBd0F0F0U1JicjtibYZEIBYYmewZmW3ViaBNQ/640?wx_fmt=png)按住 option 的右键菜单![](https://mmbiz.qpic.cn/mmbiz_png/crPesQVeyKK7xt05ZCQJjsAUP8Yv9U8Bear9tRxpbAyymd96fPJshV0U1TXutkpmMFtLuxqaIfRopA4Fnibz4bw/640?wx_fmt=png)

#### 查看 ip 地址

这是一个很常见的 “需求” 吧。有了 option 键我们就不必去 ifconfig 那么麻烦啦，直接按住 option 键点击 wifi 图标即可：![](https://mmbiz.qpic.cn/mmbiz_png/crPesQVeyKK7xt05ZCQJjsAUP8Yv9U8BSN5eXRicJAM2qJma9pjUXBzKAicxBLmiaGdjibK6VhAbNslATaCticrG9Hw/640?wx_fmt=png)option 键是个 “神奇” 的组合键，更多功能针对不同的 App 还可以自行发掘。总之，当你觉得某功能应该有，但是直接看又没看见的时候，那就试试 option 组合键吧，时常会发现惊喜哦！

### ✌ 超实用的文本快捷键

诚然，文字工作者（包括程序员）绝大部分情况下操作的都是文本内容，所以文本快捷键是最重要，必知必会的提效神器。

下面的快捷键几乎可以用于任何文本输入的地方，包括但不限于**记事本、备忘录、IDE、终端、搜索框**......

#### Home 键和 End 键

用惯快捷键的小伙伴知道，这两个键非常好用，可以说不可或缺。

*   `Home`：任意位置快速回到**行头**
    
*   `End`：任意位置快速回到**行尾**
    

不同于 Windows，纵观 Mac 的键盘并未发现这两个按键：![](https://mmbiz.qpic.cn/mmbiz_png/crPesQVeyKK7xt05ZCQJjsAUP8Yv9U8BpAI4gUTDF9aoPRcFzZUhTaOczmgxkZJoUEfWgpG6TWdknP0uVKsb0w/640?wx_fmt=png)难道如此智能的 Mac 系统不支持这种便捷操作？当然不是，它提供了组合键来达到同样的效果：

*   `fn + ←`：效果同 Home 键
    
*   `fn + →`：效果同 End 键
    

所有的**文本编辑场景**（如 txt、word、IDEA、搜索框、终端）里，这套组合快捷键都有 Home/End 的语义。但你或许可能会疑问：为何在浏览器里写文字时，有时候好使（如在输入框填写内容），有时候不好使（如在 CSDN 的编辑器里写文章），怎么回事？？？

不卖关子了，直接说根本原因：如果是在浏览器的输入框里（input、textarea 等）编辑文本，这套组合键的语义是正确的，否则语义就变为了：

*   `fn + ←`：回到页顶
    
*   `fn + →`：回到页尾
    

那么问题来了，为何在 CSDN 的编辑器里写文章（同样是编辑文本呀），怎么不好使呢？其实，本质原因是 CSDN 的 md 编辑器是用 Html 画出来的（这样才能显示图片、粗细、颜色嘛），而非输入组件：![](https://mmbiz.qpic.cn/mmbiz_png/crPesQVeyKK7xt05ZCQJjsAUP8Yv9U8Bt2riboicj77w4tIfrEKF5kHzJQQvAymEqjVNKKwOnrT2wwTI7cbsOeQA/640?wx_fmt=png)这就很容易解释为何这看起来也是在浏览器里编辑文本，但 fn 那套组合键的语义变为了页顶 / 页尾了吧。

那么怎么破？难道在类似于 CSDN 的 md 编辑器这种情况就无法实现 Home/End 了？当然不是，这时候可以使用：

*   `control + a`：效果同 Home 键
    
*   `control + e`：效果同 End 键
    

这组快捷键在文本编辑的大部分情况下和`fn + ←/→`有着相同的语义，可以通用。对此，分享下我的个人习惯是：优先使用`fn + ←/→`完成功能，只有它俩不好使了（比如 CSDN 编辑器下）才会采用`control + a/e`替补。

> 说明：笔者喜欢使用`fn + ←/→`的原因是可以配合 shift 键快速完成文本的选中，若使用`control + a/e`的话就感觉不顺手

#### 回到页顶和回到页尾

若页面很长，用鼠标 / 触摸板慢慢滚动显得效率过低，这时用这两个快捷键就更加得心应手了。

这组快捷键在浏览 “大” 网页时很常用，对应快捷键为：

*   `cmd + ↑`：回到**页顶**
    
*   `cmd + ↓`：回到**页尾**
    

这组快捷键**在浏览器里**效果等价于（👆🏻已经说了在浏览器里它俩的效果了哈）：

*   `fn + ←`：回到页顶
    
*   `fn + →`：回到页尾
    

总而言之，建议优先使用`cmd + ↑/↓`这组快捷键来表达语义。

#### 其它

翻页快捷键：

*   `fn + ↑`：相当于 Page Up
    
*   `fn + ↓`：相当于 Page Down
    

按行滚动快捷键：

*   `control + p`：上一行（相当于↑）
    
*   `control + n`：下一行（相当于↓）
    

这哥俩用得很少，毕竟使用`↑↓`方向键来得更直接。但在某些没有方向键的键盘里（如 HHKC），这对组合键就非常有用喽。

“截断式” 删除：

*   **control + k**：光标所在位置的后面内容**全部删除**，相当于截断丢弃尾部一样
    

按**单词**（根据空格识别单词分隔）移动光标：

*   `option + ←`：向左移动一个单词
    
*   `option + →`：向右移动一个单词
    

`←/→`是一位一位的移动光标，Home/End 是直接将光标干到头部 / 尾部，这是一快一慢的两个极端。然后，大多数时候我们只是需要在**中间移动**，但也希望能**快一点移动**，这个时候这组快捷键就非常好使啦，对提高移动效率的效果显著。

值得注意的是：只能按照**单词**移动。中文并不属于此行列，换句话讲：即使成千上万的中文字、中文符号都被认为是一个单词，直到遇上英文符号。因此该功能对于中文文字编辑者几乎没啥用武之地，但**对于程序员真的非常非常好用**！

终端层面快捷键
-------

终端笔者使用的 Iterm 2，shell 使用的 zsh。![](https://mmbiz.qpic.cn/mmbiz_png/crPesQVeyKK7xt05ZCQJjsAUP8Yv9U8BibAuJ2sbD6vGpjvC5Xd8zfibLuzyU19s5ZqY06xZbqQEC3egerk1ibia3Q/640?wx_fmt=png)上面介绍过的快捷键，在**终端输入**里很多都是同样的语义。但由于终端的特殊性，对某些组合键有特殊处理，所以还是有必要单独聊聊的。

### ✌ 移动光标

<table><thead><tr><th>快捷键</th><th>功能说明</th></tr></thead><tbody><tr><td>control + b</td><td>光标向左移动一个字符（相当于←）</td></tr><tr><td>control + f</td><td>光标向右移动一个字符（相当于→）</td></tr><tr><td><code>esc + b</code></td><td>光标向左移动一个单词</td></tr><tr><td><code>esc + f</code></td><td>光标向右移动一个单词</td></tr></tbody></table>

b:back，回退；f:front，前进。可以看到，在终端里按单词移动，不能使用 option 组合键了哈。

### ✌ 删除字符

<table><thead><tr><th>快捷键</th><th>功能说明</th></tr></thead><tbody><tr><td>control + h</td><td>删除光标前的 1 个字符 (效果同直接按 delete)</td></tr><tr><td>control + d</td><td>删除光标后的 1 个字符。注：无任何字符时效果同 exit</td></tr><tr><td>control + k</td><td>删除光标后所有字符</td></tr><tr><td>control + w</td><td>删除光标前的一个单词</td></tr><tr><td>control + u</td><td>删除所有字符</td></tr></tbody></table>

`control + h`和`control + d`使用←/→ + delete 键可代替且语义更清晰些；`control + u`和`control + k`是最常用的。

### ✌ 其它

<table><thead><tr><th>快捷键</th><th>功能说明</th></tr></thead><tbody><tr><td>control + L</td><td>清屏 (效果通 clear 或者<code>cmd + r</code>)</td></tr><tr><td>control + m</td><td>执行 (效果相当于回车)</td></tr><tr><td>control + r</td><td>查找之前使用过的指令</td></tr><tr><td>control + p</td><td>上条指令 (效果通↑)</td></tr><tr><td>control + n</td><td>下条指令 (效果通↓)</td></tr><tr><td>!number</td><td>指定编号执行历史记录指令 (如! 10, !33)</td></tr></tbody></table>

另外，Iterm 自己有几个快捷键平时使用还比较多的：

<table><thead><tr><th>快捷键</th><th>功能说明</th></tr></thead><tbody><tr><td>cmd + 回车</td><td>切换全屏</td></tr><tr><td>cmd + d</td><td>垂直分屏</td></tr><tr><td>cmd + shift + &nbsp;d</td><td>水平分屏</td></tr><tr><td>cmd + ;</td><td>查看历史命令</td></tr><tr><td>cmd + shift + h</td><td>查看剪贴板历史</td></tr></tbody></table>

✍总结
===

把快捷键三个字拆分开来有两部分含义：

*   **快捷**：方便触达，节省时间
    
*   **键**：使用键盘完成
    

任何东西并非越多越好，快捷键也是一样。当设置的 / 使用的快捷键非常的多，导致在使用的时候常常会想不起来使用哪个时，那么就失去了快捷的意义。为了使用而使用显然并非明智之举，一定要找到最适合自己的方式才是最好的。

使用快捷键提效没什么高深的技巧，就是先记忆，然后**多使用**，后者最为关键。用得多了，最终便能形成**肌肉记忆**，进而运用自如，方才大大提高工作效率。

最后说明一下，快捷键文章本计划用一篇分享的，但写完发现篇幅有点长，所以裁剪了一下：操作系统、终端篇；Intellij IDEA 篇。那，咱们下篇再见！

本专栏上下文
------

*   [【方向盘】超爱的 IDEA 提效神器 Save Actions，卸载了](https://mp.weixin.qq.com/s?__biz=MzI0MTUwOTgyOQ==&mid=2247495806&idx=1&sn=2b625806a5e9603d8c800f7f89459d47&scene=21#wechat_redirect)
    
*   [【方向盘】利用 IDEA 代码审查能力，来保证代码质量](https://mp.weixin.qq.com/s?__biz=MzI0MTUwOTgyOQ==&mid=2247495719&idx=1&sn=57592da18b7b92bbf737142303cf484f&scene=21#wechat_redirect)
    
*   [【方向盘】是如何高效的使用 IntelliJ IDEA](https://mp.weixin.qq.com/s?__biz=MzI0MTUwOTgyOQ==&mid=2247495600&idx=1&sn=18b428b3b27a0d0b41c4ea32e2a9e1f0&scene=21#wechat_redirect)
    

推荐阅读
----

*   [【方向盘】蚂蚁金服上市了，我不想努力了](https://mp.weixin.qq.com/s?__biz=MzI0MTUwOTgyOQ==&mid=2247489553&idx=1&sn=53ab5995a86f1780d1a10072eccf7ad5&scene=21#wechat_redirect)
    
*   [【方向盘】每人送 1G 超小容量，“BAT 网盘” 免费助你做减法](https://mp.weixin.qq.com/s?__biz=MzI0MTUwOTgyOQ==&mid=2247495234&idx=1&sn=93957a448885baa3c254bfe0b2895a8d&scene=21#wechat_redirect)
    
*   [【方向盘】因 “双减” 失业，厉经 9 面，终获美团外卖 L8 的 Offer](https://mp.weixin.qq.com/s?__biz=MzI0MTUwOTgyOQ==&mid=2247496870&idx=1&sn=f80b7b5f8dbd8e44f79c865132f07e86&scene=21#wechat_redirect)
    

![](https://mmbiz.qpic.cn/mmbiz_gif/crPesQVeyKK7xt05ZCQJjsAUP8Yv9U8BxQibxpJVFA9Slc6iaqD0aagKrTLR0znnXiaVSPPs9dTO54q3J3mUfpzbw/640?wx_fmt=gif)

> 我是[**方向盘 (YourBatman)**](https://mp.weixin.qq.com/s?__biz=MzI0MTUwOTgyOQ==&mid=2247483765&idx=1&sn=591d5e6503aed1af1eb5ed8a053aea7c&scene=21#wechat_redirect)：一个前 25 年还不会写 Hallo World、早已毕业的**大龄程序员**。网瘾失足、清考、延期毕业、房产中介、保险销售、送外卖... 是我不可抹灭的黑标签
> 
> *   👉🏻2006 - 2009：高中时期为游戏《梦幻西游》骨灰玩家
>     
> *   🎓2013.07 清考、毕业答辩 3 次未通过、延期毕业
>     
> *   🏷2013.08-2014.07 宁夏中介公司卖二手房 1 年，毕业后第 1 份工作
>     
> *   ️️🏷2014.07-2015.05 荆州 / 武汉，泰康人寿卖保险 3 月、饿了么送外卖 2 月，还有炸鸡排、直销等第 2345 份工作
>     
> *   🏷2015.08 开始从事 Java 开发，闯过外包，呆过大厂！多年架构经验，任基础架构 / 中间件团队负责人
>     
> *   🏷2021.08 [因 “双减政策” 失业！历经 9 面，终获美团外卖 L8 的 offer](https://mp.weixin.qq.com/s?__biz=MzI0MTUwOTgyOQ==&mid=2247496870&idx=1&sn=f80b7b5f8dbd8e44f79c865132f07e86&scene=21#wechat_redirect)
>     
> *     
>     
> *   🙅🏻‍♀️Java 架构师、Spring 开源贡献者、CSDN 博客之星年度 Top 10、领域建模专家、写作大赛 1/2 届评委
>     
> *   📚将出版书籍`《Spring奇淫巧技》`，致力于很好、很具深度、很实用的专栏，为市场贡献微薄之力
>     
> *     
>     
> *   现在写纯粹技术专栏（公号后台回复**专栏列表**），不哗众取宠。如果你也有共鸣，可加我好友 (fsx1056342982) 一起进步
>     

![](https://mmbiz.qpic.cn/mmbiz_png/crPesQVeyKK7xt05ZCQJjsAUP8Yv9U8BN8sL6GegBibOP9NshUwr4e3wicpOUmKficMiasaxjM8krNFNZ7X8LYkJ9g/640?wx_fmt=png)

<table><thead><tr><th>序号</th><th>专栏名称</th><th>简介</th></tr></thead><tbody><tr><td>01</td><td><a href="https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0MTUwOTgyOQ==&amp;action=getalbum&amp;album_id=1439863091768164353#wechat_redirect" data-linktype="2" wah-hotarea="click">【方向盘】- 程序人生</a></td><td>程序人生，人生程序</td></tr><tr><td>02</td><td><a href="https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0MTUwOTgyOQ==&amp;action=getalbum&amp;album_id=1608175058441551875#wechat_redirect" data-linktype="2" wah-hotarea="click">【方向盘】- 资讯 / 新特性</a></td><td>IDEA、JDK、Spring 技术栈...... 新特性</td></tr><tr><td>03</td><td><a href="https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0MTUwOTgyOQ==&amp;action=getalbum&amp;album_id=1374601107946962947#wechat_redirect" data-linktype="2" wah-hotarea="click">【方向盘】-IntelliJ IDEA</a></td><td>熟练使用 IDEA 就相当拥有物理外挂，助你高效编码</td></tr><tr><td>04</td><td><a href="https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0MTUwOTgyOQ==&amp;action=getalbum&amp;album_id=1630480921197887488#wechat_redirect" data-linktype="2" wah-hotarea="click">【方向盘】-Bean Validation</a></td><td>熟练掌握数据校验，减少 90% 的垃圾代码</td></tr><tr><td>05</td><td><a href="https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0MTUwOTgyOQ==&amp;action=getalbum&amp;album_id=1696358010555547649#wechat_redirect" data-linktype="2" wah-hotarea="click">【方向盘】- 日期时间</a></td><td>帮你解决 JDK Date、JSR 310 日期 / 其实 的一切问题</td></tr><tr><td>06</td><td><a href="https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0MTUwOTgyOQ==&amp;action=getalbum&amp;album_id=1727592481828978689#wechat_redirect" data-linktype="2" wah-hotarea="click">【方向盘】-Spring 类型转换</a></td><td>Spring 类型转换 - 框架设计的基石</td></tr><tr><td>07</td><td><a href="https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0MTUwOTgyOQ==&amp;action=getalbum&amp;album_id=1727601376051937282#wechat_redirect" data-linktype="2" wah-hotarea="click">【方向盘】-Spring static</a></td><td>static 关键字在 Spring 里的应用</td></tr><tr><td>08</td><td><a href="https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0MTUwOTgyOQ==&amp;action=getalbum&amp;album_id=1907899736917884936#wechat_redirect" data-linktype="2" wah-hotarea="click">【方向盘】-Cors 跨域</a></td><td>关于跨域请求问题，本专栏足矣</td></tr><tr><td>09</td><td><a href="https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0MTUwOTgyOQ==&amp;action=getalbum&amp;album_id=1502124864084721666#wechat_redirect" data-linktype="2" wah-hotarea="click">【方向盘】-Jackson</a></td><td>Almost Maybe 是最好的 Jackson 专栏</td></tr><tr><td>10</td><td><a href="https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0MTUwOTgyOQ==&amp;action=getalbum&amp;album_id=1727585247963201537#wechat_redirect" data-linktype="2" wah-hotarea="click">【方向盘】-Spring 配置类</a></td><td>专讲 @Configuration 配置类，你懂的</td></tr><tr><td>11</td><td><a href="https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0MTUwOTgyOQ==&amp;action=getalbum&amp;album_id=1914508501259730944#wechat_redirect" data-linktype="2" wah-hotarea="click">【方向盘】-Spring 技术栈</a></td><td>暂无所属小分类的，Spring 技术栈大分类</td></tr><tr><td>12</td><td><a href="https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0MTUwOTgyOQ==&amp;action=getalbum&amp;album_id=1914519040555827201#wechat_redirect" data-linktype="2" wah-hotarea="click">【方向盘】-JDK</a></td><td>暂无所属小分类的，JDK 技术栈大分类</td></tr><tr><td>13</td><td><a href="https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0MTUwOTgyOQ==&amp;action=getalbum&amp;album_id=1932581527213654017#wechat_redirect" data-linktype="2" wah-hotarea="click">【方向盘】-Servlet</a></td><td>Servlet 规范、Web 相关内容专题</td></tr><tr><td>14</td><td><a href="https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0MTUwOTgyOQ==&amp;action=getalbum&amp;album_id=1940957171895058444#wechat_redirect" data-linktype="2" wah-hotarea="click">【方向盘】-Java EE</a></td><td>从 Java EE 到 Jakarta EE，30 年弹指一挥间</td></tr><tr><td>15</td><td><a href="https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0MTUwOTgyOQ==&amp;action=getalbum&amp;album_id=2155828498790170627#wechat_redirect" data-linktype="2" wah-hotarea="click">【方向盘】-Spring Boot 新特性</a></td><td>Spring Boot 中、大版本发布时的新特性系列专题</td></tr><tr><td>16</td><td><a href="https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0MTUwOTgyOQ==&amp;action=getalbum&amp;album_id=2155830285110689794#wechat_redirect" data-linktype="2" wah-hotarea="click">【方向盘】-Spring Framework 新特性</a></td><td>Spring Framework 中、大版本发布时的新特性系列专题</td></tr><tr><td>17</td><td><a href="https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0MTUwOTgyOQ==&amp;action=getalbum&amp;album_id=2155831184403021826#wechat_redirect" data-linktype="2" wah-hotarea="click">【方向盘】-Spring Cloud 新特性</a></td><td>Spring Cloud 中、大版本发布时的新特性系列专题</td></tr><tr><td>...</td><td>...</td><td>...</td></tr><tr><td>99</td><td><strong>源代码库</strong></td><td>大多数专栏均配有源代码，都在这里</td></tr></tbody></table>

*   源代码库地址：https://github.com/yourbatman/tech-column-learning
    
*   CSDN 主页：https://blog.csdn.net/f641385712
    
*   掘金主页：https://juejin.cn/user/430664289367192
    
*   博客园主页：https://www.cnblogs.com/yourbatman
    
*   个人博客主页：https://yourbatman.cn
    
*   个人网盘主页：https://wangpan.yourbatman.cn