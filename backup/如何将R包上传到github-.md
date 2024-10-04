### 什么是 GitHub？

[[GitHub](https://github.com/)是一个用于存放 [git](https://git-scm.com/)存储库的网站。它有点像程序员（和数据科学家）的 Facebook：每个人都在上面；你可以查看他们正在做什么，轻松浏览他们的代码并提出建议或更改。

GitHub 降低了协作的门槛。通过 GitHub，您可以轻松为他人的代码提供修改建议，而他人也可以轻松采纳您的修改建议。

### 为什么要把你的 R 包放在 GitHub 上？

将您的 R 包放在 GitHub 上有许多好处。

- 其他人可以更轻松地阅读您的代码。他们可以在 Web 浏览器中进行阅读，而无需下载、提取并开始搜索。

- GitHub 包含问题跟踪：人们（包括您自己）可以记录他们遇到的问题或他们希望您提出的改进建议。

- 除了指出问题之外，人们还可以修复问题并向您发送补丁，您可以轻松测试该补丁并将其合并到您的软件包中。其中一些可以完全在线完成，无需了解 git。他们不会说“您的文档中有错别字”，而是说“这里，我已修复了您的文档中的错别字。”

- 借助[[Hadley Wickham](https://hadley.nz/)]的 [[devtools](https://github.com/hadley/devtools)的`install_github()`软件包中的功能 ，人们可以轻松地直接从 GitHub 安装您的软件包。它不必在[[CRAN](https://cran.r-project.org/)上。（正如 [[您所看到的](https://kbroman.org/pkg_primer/pages/cran.html)，在 CRAN 上获取您的软件包可能有点困难。）

要将自己写的R包上传到GitHub，可以按照以下步骤进行操作：

## 怎么将R包放在Github上

### **准备工作**

1. **创建GitHub账户**：如果还没有GitHub账户，需要先注册一个。

2. **安装Git和RStudio**：确保本地已经安装了Git和RStudio。

### **步骤一：创建R包**

3. **在RStudio中创建新项目**：

  - 打开RStudio，点击“File” -> “New Project…” -> “New Directory” -> “R Package”。

  - 输入包的名称和保存路径，勾选“Create a git repository”选项，然后点击“Create Project”。

4. **编辑DESCRIPTION文件**：

  - 在RStudio中打开DESCRIPTION文件，填写包的名称、版本号、作者信息等。

### **步骤二：初始化Git仓库**

5. **在RStudio中初始化Git**：

  - 打开RStudio的“Terminal”面板（快捷键：Alt + Shift + T），输入以下命令初始化Git仓库：

```Shell
git init #貌似不是必须的
```


6. **添加文件到Git仓库**：

  - 在Terminal中输入以下命令，将所有文件添加到Git仓库：

```Shell
git add .
git commit -m "Initial commit"
```


### **步骤三：在GitHub上创建仓库**

7. **创建新的GitHub仓库**：

  - 登录GitHub，点击右上角的“+”按钮，选择“New repository”。

  - 输入仓库名称（建议与R包名称一致），填写描述信息，选择“Public”或“Private”，然后点击“Create repository”。

8. **将本地仓库连接到GitHub**：

  - 在GitHub仓库页面，复制仓库的HTTPS或SSH地址。

  - 在RStudio的Terminal中输入以下命令，将本地仓库与GitHub仓库关联：

```Shell
git remote add origin https://github.com/bionoob7/scMisc #github项目地址
git branch -M main #修改master分支名字为main！！！十分重要
git push -u origin main
```


### **步骤四：推送代码到GitHub**

9. **推送代码**：

  - 在RStudio的Terminal中输入以下命令，将代码推送到GitHub：

```Shell
git push origin main
```


### **步骤五：维护和更新**

10. **更新代码**：

  - 每次对R包进行修改后，使用以下命令提交和推送更新：

```Shell
git add .
git commit -m "描述此次更新的内容"
git push origin main
```


通过以上步骤，你就可以将自己写的R包上传到GitHub，并且可以方便地进行版本控制和协作开发

### README（补充）

为了更好地展示你的R包，建议在GitHub仓库中添加一个 `README.md` 文件，描述你的R包的功能、使用方法等。你还可以添加一个 `LICENSE` 文件，声明你的R包的开源许可证。

```Shell
echo "# My R Package" >> README.md
git add README.md
git commit -m "Add README.md"
git push origin main
```


### 为你的R包配置一个网站（补充）

您可能还想为您的软件包创建一个网站。使用[[GitHub Pages](https://pages.github.com/) 很容易做到这一点。请看[为 R/qtlcharts 软件包创建的网站](https://kbroman.org/qtlcharts)。为你的软件包 git 仓库创建一个空的`gh-pages`分支，然后填充一个网站。GitHub 可以让网站轻松使用[Jekyll Bootstrap](http://jekyllbootstrap.com/)，因此你可以用`Markdown`而不是`HTML`来编写内容。请看这里的[简单网站](https://kbroman.org/simple_site)教程，尤其是[制作项目网站的](https://kbroman.org/simple_site/pages/project_site.html)那一页。