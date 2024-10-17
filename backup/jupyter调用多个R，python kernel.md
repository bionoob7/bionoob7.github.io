jupyter notebook是非常流行的调试代码方式，对于R和python这种脚本语言能时时反馈每一行代码的运行结果，对于调试画图代码很便捷，而且能够**有效记录数据分析过程**。jupyter支持R kernel (已经放弃Rstudio许久)，有时候我们有多个R和python安装路径（例如多个conda环境下的R），本文记录的是jupyter调用多个kernel的设置步骤。

## 启动jupyter服务

```Shell
# 建立jupyter配置文件目录
mdkir -p ~/.jupyter
# 设置密码
jupyter lab password
# 建立文件目录和设置密码的步骤只需要做一次
# 启动jupyter
# --notebook-dir参数可以自定义jupyter的启动路径，对于打开和保存文件便利
jupyter lab --port=1031 --notebook-dir=/seqyuan/project/
```


不想用时可以`control + c` 停掉服务，或直接关掉terminal界面；

如果jupyter服务建立在服务器上，想长期使用，下面可以确保在关掉terminal的时候服务仍然运行 `nohup jupyter lab --port=1008 --notebook-dir=/seqyuan/project/ &`

## 添加python kernel

启动jupyter服务的时候，默认会把jupyter所在环境的python加到**jupyter kernel list**里，不需要重复添加，如果想要把其他python加入到jupyter kernel，对应的python先安装ipykernel这个包

```Shell
/customPYpath/envs/seurat4/bin/pip install ipykernel
# 下面就是添加 kernel代码
# --display-name 参数可以自定义设置要展示的kernel名称
/customPYpath/envs/seurat4/bin/python -m ipykernel install \
  --user --name seurat4_py --display-name "seurat4py"
```


## 添加R kernel

如果想要把R加入到jupyter kernel list，对应的R要先安装IRkernel这个包

```R
install.packages('IRkernel')
# devtools::install_github('IRkernel/IRkernel')
```


```Shell
# 在启动jupyter的user 同环境下打开R命令行
/customPYpath/envs/deeptools/bin/R
```


```R
# 可以给kernel命名和展示名称可以不一致
IRkernel::installspec(name='R4deeptools', displayname='deeptools_R')
```


> 有一点要特别注意：添加kernel时python和R的添加kernel命令一定要和启动jupyter的环境和user一致，不然会导致添加不上。

使用上面的方法，任何R和python路径都可以被添加到jupyter的kernel list，只要有IRkernel（或ipykernel）包就行，另外就是注意命名要区别开。

## 使用jupyter服务

### **本地电脑建立的jupyter服务**

可以在浏览器输入`localhost:1031`即可使用jupyter服务，

添加新的软件kernel不需要关闭jupyter服务，在新kernel添加之后，点击`红圈的+`号即可看到新增的R在kernel列表里了。

![image.png](https://tc-cdn.flowus.cn/oss/9c28db19-18e9-49fe-bb73-a59230613a19/image.png?time=1729129500&token=edccc657350b669d19615ddf1c8c2ae0b6a5f7a2c2d3673b9351bd15d2091baa&role=free)

### **在linux服务器建立的jupyter服务访问方法**

在浏览器输入的应该是服务器的IP地址+端口， `IP:1031`

## 使用注意事项

有时候服务器会打开很多notenooks，这些notebooks有时会load比较大的数据，积累多了容易使服务器卡顿，需要及时关闭不需要使用的kernel，可以通过下图中红圈所示的按钮查看在后台运行的notebook，选择性及时关闭。

![image.png](https://tc-cdn.flowus.cn/oss/add3fa61-e5ee-4c17-9e5a-f13e179aa42d/image.png?time=1729129500&token=9c9208cb793f48106084c9084808f2a2e5f015edfb8dc4234d98b83dc2cb8c31&role=free)




