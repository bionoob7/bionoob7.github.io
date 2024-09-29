安装好R和RStudio之后，鼠标左键双击RStudio的图标，即可打开RStudio了。

我们可以先试一下R的计算功能，比如做一些加减乘数等数学运算。

> 在编写代码前，请一定要记住**必须把输入法改为英文模式**，尤其是各种标点符号！

## 控制台和脚本窗口

在`Console`（中文：控制台）窗口中，大于号`>`的后面，输入`1+1`，然后按回车键，就会给出计算结果。同理输入其他运算式，按回车键，都会立马得到结果。

![https://mmbiz.qpic.cn/mmbiz_png/tpAC6lR84R8Bd79xC88eibtve4h15CflNxmMWNicjX52SpnhEmxs0UtcjB3qHRBvDK25SQu7UpZ7DCMxF3dE9IKQ/640?wx_fmt=png&from=appmsg](https://mmbiz.qpic.cn/mmbiz_png/tpAC6lR84R8Bd79xC88eibtve4h15CflNxmMWNicjX52SpnhEmxs0UtcjB3qHRBvDK25SQu7UpZ7DCMxF3dE9IKQ/640?wx_fmt=png&from=appmsg)

> 这个大于号`>`叫**命令提示符**，提示你这里是输入代码的地方。

R语言中的一些常见数学运算：

```R
# + - * /
1+1
1-1
3*2
10 / 2

abs(-10) # 绝对值
sqrt(25) # 平方根
log(100)
log10(100)
log2(100)
exp(10) # 指数函数

ceiling(3.257) # 往上取整数
floor(3.257) # 往下取整数
round(3.275, digits = 2) # 四舍五入
```


除了数学运算，还可以输入各种函数，比如输入`print("Hello, world")`，你可以尝试下。

但是通常我们是不会直接在控制台窗口写代码的，而是在**脚本窗口**写代码，控制台用来查看代码输出结果。R脚本文件的扩展名为.R(或者.r)。

以下是两种方式新建一个R脚本：

方法一：依次点击左上角的File-New file-R Script，即可打开一个新的R脚本：

![https://mmbiz.qpic.cn/mmbiz_jpg/tpAC6lR84R8Bd79xC88eibtve4h15CflN1dbada8EicY7ra69wB8u1YpsP369NmUP0dtobqheJkTqABqpv2OC0Qw/640?wx_fmt=jpeg](https://mmbiz.qpic.cn/mmbiz_jpg/tpAC6lR84R8Bd79xC88eibtve4h15CflN1dbada8EicY7ra69wB8u1YpsP369NmUP0dtobqheJkTqABqpv2OC0Qw/640?wx_fmt=jpeg)

方法二：点击New File小图标，再点击R script，也可以新建一个R脚本，两种方法完全一样。

![https://mmbiz.qpic.cn/mmbiz_png/tpAC6lR84R8Bd79xC88eibtve4h15CflNk3sE0b1v1P13YyOQd1uibSL2qpicMOyP5icrZuzFguE6fDqpDuEMhkEWw/640?wx_fmt=png&from=appmsg](https://mmbiz.qpic.cn/mmbiz_png/tpAC6lR84R8Bd79xC88eibtve4h15CflNk3sE0b1v1P13YyOQd1uibSL2qpicMOyP5icrZuzFguE6fDqpDuEMhkEWw/640?wx_fmt=png&from=appmsg)

新建脚本文件后，我们就可以在脚本中写自己的代码了，这里写好的代码可以保存的，方便以后使用。如果你没保存，会有`*`提示，下面的保存按钮可以保存当前脚本（快捷键“control+s”），保存时可以修改这个脚本的名字：

![https://mmbiz.qpic.cn/mmbiz_png/tpAC6lR84R8Bd79xC88eibtve4h15CflNtL4aI82QTOJK8d3PkXnswarBMxEmP3eRABIOTMOJ8x5xlXLW7gxuMg/640?wx_fmt=png&from=appmsg](https://mmbiz.qpic.cn/mmbiz_png/tpAC6lR84R8Bd79xC88eibtve4h15CflNtL4aI82QTOJK8d3PkXnswarBMxEmP3eRABIOTMOJ8x5xlXLW7gxuMg/640?wx_fmt=png&from=appmsg)

在这个脚本文件里可以写任何你想写的代码，比如上面我写了一个名字为`fruits`的向量，其中包括3个元素：apple、banana、watermelon。

R语言中使用`<-`作为复制符号，而不是`=`（但是使用`=`也可以，没有任何问题），以下两种写法都可以：

```R
# 两种写法都可以
a <- 3
a = 3

b <- 4
b = 4

a+b
```


> 上图脚本中前两行我写了很多中文，而且在每一行的前面都加了`#`，`#`表示这一行不是代码，不需要运行，你可以在`#`后面写任何你想写的东西，比如一些解释说明的话等。

写好代码之后如果你要运行这个代码的话只需要把光标位置放在你要运行的代码的这一行，然后点击`Run`即可运行这行代码（也可以使用快捷键“control+enter”），代码运行结果会出现在**控制台窗口**：

![https://mmbiz.qpic.cn/mmbiz_png/tpAC6lR84R8Bd79xC88eibtve4h15CflNYFias75MbdWyPORBhTEeZx3dC3ZFCkAlp827uibjeXcXz1cof0l4XkMA/640?wx_fmt=png&from=appmsg](https://mmbiz.qpic.cn/mmbiz_png/tpAC6lR84R8Bd79xC88eibtve4h15CflNYFias75MbdWyPORBhTEeZx3dC3ZFCkAlp827uibjeXcXz1cof0l4XkMA/640?wx_fmt=png&from=appmsg)

点击Run之后，代码运行结果会出现在控制台窗口：

![https://mmbiz.qpic.cn/mmbiz_png/tpAC6lR84R8Bd79xC88eibtve4h15CflNicCpYD3YEjYHwuGOL5T9aibOaNib2gHwP4J22cQdIPMicwTiaTKRTKpgbeQ/640?wx_fmt=png&from=appmsg](https://mmbiz.qpic.cn/mmbiz_png/tpAC6lR84R8Bd79xC88eibtve4h15CflNicCpYD3YEjYHwuGOL5T9aibOaNib2gHwP4J22cQdIPMicwTiaTKRTKpgbeQ/640?wx_fmt=png&from=appmsg)

> 光标位置在代码的开头、中间、结尾都没关系，RStudio都能识别出来，你也可以直接使用鼠标全部选中你要运行的代码，再点击Run，也可以达到一样的效果。

如果命令提示符（也就是控制台窗口中的`>`）变成了一个加号（+），这表示我们输入的代码不完整，它正在等你输入完整的代码，这种情况通常是因为我们输入的代码太长了，超过了1行的限制，或者代码中的标点符号（比如：括号，逗号等）不成对。此时按“Esc”即可退回到正常状态下。

RStudio的一大优点就是有代码自动补全和语法高亮功能。

当你输入代码时，编辑器会提供语法高亮和代码补全提示。你只要输入前3个字母，再按Tab键，即可显示所有可能的函数，按上下方向键可以选择你需要的函数，选好之后按enter键即可选中。鼠标停留在函数名字上即可显示该函数的用法：

![https://mmbiz.qpic.cn/mmbiz_png/tpAC6lR84R8Bd79xC88eibtve4h15CflNpAkuoxdO0Usxs4gVJ4HibANvevHoC7Pn812aSsC2haibmupWKuKqOtdA/640?wx_fmt=png&from=appmsg](https://mmbiz.qpic.cn/mmbiz_png/tpAC6lR84R8Bd79xC88eibtve4h15CflNpAkuoxdO0Usxs4gVJ4HibANvevHoC7Pn812aSsC2haibmupWKuKqOtdA/640?wx_fmt=png&from=appmsg)

另一个常用的快捷键是赋值符号：`<-`，它是由小于号`<`和减号`-`组成的，在RStudio中可以通过快捷键`Alt+-`快速输入。

## 环境窗口和绘图窗口

当我们运行代码后，我们建立的对象（比如上面的`fruits`、`x`、`y`）会出现在**Enviroment**窗口中：

![https://mmbiz.qpic.cn/mmbiz_png/tpAC6lR84R8Bd79xC88eibtve4h15CflNTlcJwRsiatiaSoRSe7AxnXeonXfzQB3bz0icMvLUwRsh5SEPjjBic1SFWQ/640?wx_fmt=png&from=appmsg](https://mmbiz.qpic.cn/mmbiz_png/tpAC6lR84R8Bd79xC88eibtve4h15CflNTlcJwRsiatiaSoRSe7AxnXeonXfzQB3bz0icMvLUwRsh5SEPjjBic1SFWQ/640?wx_fmt=png&from=appmsg)

当我们执行画图代码后，图形会出现在**Plots**窗口：

![https://mmbiz.qpic.cn/mmbiz_png/tpAC6lR84R8Bd79xC88eibtve4h15CflNtgSfFD3sAunhA3Ccv0l8cetApOr3oM09fS69VLsuHwV3ey1wWFjLpQ/640?wx_fmt=png&from=appmsg](https://mmbiz.qpic.cn/mmbiz_png/tpAC6lR84R8Bd79xC88eibtve4h15CflNtgSfFD3sAunhA3Ccv0l8cetApOr3oM09fS69VLsuHwV3ey1wWFjLpQ/640?wx_fmt=png&from=appmsg)

清除当前环境中的某个对象，可以用：

```R
rm(xxx) # xxx是对象名字，rm是remove的缩写
```


如果要同时清除所有对象，可以直接点击Enviroment窗口中的小扫帚图标，也可以直接用：

```R
rm(list = ls()) # ls()是list的缩写，意思是列出当前所有对象
```


## 推荐的RStudio设置

点击`Tools`-`Global Options`，按照以下图片所示进行设置。

![https://mmbiz.qpic.cn/mmbiz_png/tpAC6lR84R8Bd79xC88eibtve4h15CflNydXOiabG0cxTXhz8eLu6EvNf08Pm2QFRVoKaFjic3y7ibwFl06nINFjBg/640?wx_fmt=png&from=appmsg](https://mmbiz.qpic.cn/mmbiz_png/tpAC6lR84R8Bd79xC88eibtve4h15CflNydXOiabG0cxTXhz8eLu6EvNf08Pm2QFRVoKaFjic3y7ibwFl06nINFjBg/640?wx_fmt=png&from=appmsg)

![https://mmbiz.qpic.cn/mmbiz_png/tpAC6lR84R8Bd79xC88eibtve4h15CflNIqfZibqRqEbTEUbzJY95O8yq4xcU4eicic6bbOv9dxd5Pcy8P9u5E9QGA/640?wx_fmt=png&from=appmsg](https://mmbiz.qpic.cn/mmbiz_png/tpAC6lR84R8Bd79xC88eibtve4h15CflNIqfZibqRqEbTEUbzJY95O8yq4xcU4eicic6bbOv9dxd5Pcy8P9u5E9QGA/640?wx_fmt=png&from=appmsg)

