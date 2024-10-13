**写一个R包，大概要经过以下几个步骤：**

1）准备一个R包，含有一些必需文件和文件夹。
2）准备R包需要用到的函数和测试数据，并写函数和测试数据的注释。
 3）将函数封装成一个包。
 4）写说明书。（非必须）
 5）将R包上传到github，方便修改和维护。（非必须）
 6）补充：遇到问题

以下介绍各个步骤的要点：

### **1、准备一个R包**

在介绍这个之前，需要先了解一下**R包的结构。** 我们可以通过Rstudio来获得一个基本的R包模板，模板里就是R包的结构和必需内容。通过Rstudio→File→New Project→New Directory→R package，填写R包名称和创建路径，获得一个名为“myPackage”的R包，里面有以下的文件或文件夹，是一个R包必须有的：

myPackage（见图1）
   | ------  man文件夹：用于存放.Rd文件的文件夹，里面有一个示例文件hello.Rd。
    | ------  R文件夹：用于存放R函数的文件夹，里面有一个示例文件hello.R。
    | ------  DESCRIPTION: R包的描述文件。
    | ------  NAMESPACE：用来指定R包输入和输出的包，还有一些特殊的名称。

   此外，以后如果想要加载这个包的编辑环境，可以用Rstudio→File→Open Project加载myPackage.Rproj文件，很方便就可以加载环境。

![https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj1jXLLOUr8HmTGV6TwCItm-OWeX2uLGxpkura-I5eoyUpOxfMDVwbJOpGRb0r7Nqj_CXuCZ88ya9DvGxawY1TboC4VOJ-ExbhKs3on37CK1UrPDpgUXWv3iX7gRZlht9IIDJfK1EYioVzT/s640/%25E6%259C%25AA%25E6%25A0%2587%25E9%25A2%2598-1.png](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj1jXLLOUr8HmTGV6TwCItm-OWeX2uLGxpkura-I5eoyUpOxfMDVwbJOpGRb0r7Nqj_CXuCZ88ya9DvGxawY1TboC4VOJ-ExbhKs3on37CK1UrPDpgUXWv3iX7gRZlht9IIDJfK1EYioVzT/s640/%25E6%259C%25AA%25E6%25A0%2587%25E9%25A2%2598-1.png)

图1：R包的创建和结果

其中，由两个部分不需要做任何人工改动：
1）NAMESPACE 命名空间文件，最好不要经过人工编辑，roxygen2包可以帮助你新建这个文件，建议你先把这个文件删掉，否则没办法新建。
 2）man文件夹，存放.Rd文件，全部可以由roxygen2包自动生成，不经过人工编辑。

以下是需要人工进行修改的部分：

#### **1.1 R文件夹**

这个文件夹用来存放你的所有R函数。如果你的包使用了其他的语言，需要新建一个src文件夹来分开存放。详见[参考](https://bookdown.org/yihui/r-ninja/r-package.html)。

#### **1.2 DESCRIPTION文件**

这个文件描述R包的重要信息，详细可以参照Hadley Wickham[R包描述部分说明](http://r-pkgs.had.co.nz/description.html)，里面包含内容用名称+冒号+空格为开头。我们就根据Rstudio生成的模板进行修改即可，里面的项目和内容，基本上都能见名知义。可能会影响后面检查包（check）出现warning或note的地方如下：

**1） Depends**

格式为R+空格+左括弧+大于号+等于号+空格＋版本号

如果你想声明R (>= 3.4.3)，需要写成R (>= 3.4.0), 否则会出现警告：Dependence on R version '3.4.3' not with patchlevel 0。

另外，depends可以写R版本，以及你需要声明特殊版本号的R包，如果不需要就仅写R版本即可。否则check时会出现提示。

import和suggest主要取决于这个包是否参与你包的主要功能，如果是一些辅助功能，有了这个包你的软件功能会更加强大，可以放在suggest里。

**2）每个内容末尾句号的问题**
除了Description末尾必须有实心句号外，其他所有，包括Title 末尾均不能有句号，否则就会出现警告。（这点网上也有人吐槽，这不合理，标题也应该是完整的句子。）

**3）Encoding**
该文件里必须声明 Encoding: UTF-8，没有这一行就会报错。

**4）License**
该文件里必须声明License，不知道选哪个可以参考[R CRAN上关于License的说明](https://www.r-project.org/Licenses/)。

还需要注意的是这个文件，包括后面的R脚本注释，最好不要超过每行80个字符，否则在自动生成注释文件和manual的时候会超出边框，check的时候也会警告。

### **2、准备R包需要用到的函数和测试数据，并写函数和测试数据的注释。**

这一部分是R包的核心。关于R包函数怎么写笔者就不详述了，这里介绍一下如何写函数的注释。原本在写R包的时候，需要些一些后缀为rd的文件作为函数的说明文件，工作量很大，又需要学习latex，很不方便。自从有了roxygen2（roxygen现在已经不更新了，请安装roxygen2）这个R包，写rd文档就非常方便了。只需在R函数前面写一段话格式化的文字就可以。

建议使用Rstudio来写，因为它会把注释重要的地方标亮，还会提示错用的特殊符号。

#### 2.1 编写注释

比如一个R函数和注释如下，虽然不是每一个注释都需要写成这样，但是这里为了讲解，写的具体一点：

```R
#' @title Poisson vector.
#' @description  Creating poisson vector.
#' @details Input an integer and return the log density of a poisson distribution with lambda equals the input integer.
#' @param x A non-negative integer. or vector.
#' @return A numeric vector of log density.
#' @export
#' @import stats
#' @importFrom stats dpois
#' @examples
#' poiVec(5)
#' poiVec(6)
poiVec <- function(x) {
  dpois(x , x)
}

```


1）注释的每一行开头用#'或者##'开始。注释基本上都是完整的句子，请用实心句号结束。

2）注释的内容和格式，每一个项目和其内容之间用空格隔开：

@title 定义函数的标题，这个标题里的词可以搜索到

@description 是函数的描述，如果描述内容很长，则可以写在@details里。

@param 声明函数的参数，每个参数都需要声明，如果用一行同时声明2个输入类型一致的
参数，则用@param x,y 来表示，参数之间只用逗号隔开，不能有空格。
 @return 非必须，声明返回的内容和类型，如果没有返回就不需要加这个项，否则会报错。

@export 非必须，如果需要输出该函数到NAMESPACE里，则必须加上这个项

@import 和@importFrom 虽然不是必须的，但是非常需要注意，如果你的函数里用到了其他包的函数，最好声明一下，当然基本的函数可以不用声明。这些内容会写到NAMESPACE文件里。如果不声明的话，在check包的时候，就会因为运行不了函数的例子而报错。

而且不建议在函数里加library()或者require()这种命令，也不建议在函数里使用包名::函数名这种命令，最好通通写在import和importFrom里，这样在生成rd文件的时候就被roxygen2自动输出到NAMESPACE文件中，方便维护和修改。

@examples 也不是必须的，但是一旦你写了，就要保证里面的脚本能够运行。所以一定要声明好import的包。还有如果example里用到了其他的包，需要写require(包名)，否则check的时候会找不到这个包。

3）需要注意格式化，这里只简单地列举一些，详细见Hadley Wickham写的[Format说明](https://cran.r-project.org/web/packages/roxygen2/vignettes/formatting.html)：

斜体   \emph{italics}

加粗：   \strong{bold}

标注R函数和代码：   \code{r_function_call(with = "arguments")}

标注NULL、TRUE或FALSE：    \code{NULL}

标注R包 ：    \pkg{package_name}

引用本R包中的函数说明 ：   \code{\link{function}}

引用其他R包里的函数说明，如MASS包中的abbey：    \code{\link[MASS]{abbey}}

插入链接：   \url{http://rstudio.com}

给某个字段插入链接 ：  \href{http://rstudio.com}{Rstudio}

插入email，注意要写两个@：    \email{*@@rstudio.com}

4）如果想要在一个注释里添加多个函数，可以用@rdname, 而且一定要记得这个函数也要export，否则运行例子的时候如果写了这个函数，就会因为找不到函数而报错。

```R
# ' @rdname 写了注释的那个函数的名称

# ' @export

```


5）注释的内容最好不要超过80个字符每行，否则在生成manuel.pdf文件的时候就会超出边界。Rstudio有一个[工具](https://github.com/tjmahr/WrapRmd)可以帮助我们自动换行：

devtools::install_github("tjmahr/WrapRmd")

安装这个包以后，在Rstudio里面，选中注释然后Ctrl/Cmd + Shift + /  就可以自动换行。

6）注释因为是转成rd文件，有一些需要转义的符号，比如@号，\号，{}和[]都是latex里的重要符号，都需要转义。

#### 2.2 生成rd文件

用roxygen2包的roxygenize函数即可生成rd文件。生成前最好先把之前Rstudio创建的NAMESPACE文件删掉。如果注释写错了就会报错，这一步需要反复修改。

roxygen2::roxygenize(package.dir = ".")

### **3、创建R包**

#### 3.1 check

在build之前，需要先check一下R包有没有问题，我们可以打开命令行工具，用以下命令来check。

R CMD check RPkgName

不过这里**推荐使用devtools**来进行R包的检查和创建：

devtools::check()

如果想检查manual，可以运行下面的命令，就会在temp文件夹里生成一个pdf文件：

devtools::check(manual = TRUE)

这一步可能会遇到很多的error和warning，这都是因为之前的文件没有写对导致的。

笔者遇到的报错或者warning有：

1） 警告：no visible binding for global variable.

这个warning最好还是解决一下，比如ggplot2就经常出这个warning。可以aes改成aes_string， 然后x和y就可以传入字符串了。还有比如你在load Rdata的时候没有给Rdata赋值，之后直接调用也会报这个警告。

2）警告：no visible global function definition for '函数名'

这可能是因为你调用了其他包的函数，但是没有在NAMESPACE里声明（也就是没有在rd注释的import里声明）。也可能是你写的函数没有export到NAMESPACE里。

3）报错：checking examples 时“could not find function  ‘函数名’”

如果上述问题2）没有解决，那运行example的时候就会遇到这个报错。如果解决了，还遇到报错，那就可能是你的example里调用了其他包，你没有声明require(包)。

这步需要反复地修改和check，尽量修改到没有报错和警告为止。笔者改到出现如下结果的时候差点没有哭出来。（1 note是因为笔者的手册和测试数据太大了。）

0 errors √ | 0 warnings √ | 1 note x

#### 3.2 Build

在确保R包没有错误之后，便可以创建了。你可以打开命令行工具，cd到R包文件夹所在的目录，然后build R包。它默认会创建vignettes和manual，然后打包成*.tar.gz。

R CMD build RPkgName

然后安装R包：

R CMD INSTALL RPkgName.tar.gz

不过这里推荐使用devtools来进行R包的build和install。

devtools::build()

会创建一个*.tar.gz, 然后

devtools::install()

则可以安装R包。

如果你的R包由vignettes，则为了能够使用browseVignettes()命令看到你的vignettes，需要build vignettes：

devtools::build_vignettes()
devtools::build_manual(pkg = ".", path = './doc')
 devtools::build(vignettes = TRUE, manual = TRUE)

并且用以下命令安装

devtools::install(build_vignettes = TRUE)

这样就可以看到这个R包的vignette。

browseVignettes(RPkgName)

### **4、写说明书**

这部分并不是R包的必要组成部分，但是有一个使用说明书能够让别人方便的使用，也方便自己回顾。很多人都把vignettes写的像文献一样详细和严谨。

在R包主目录里新建一个名为vignettes的文件夹（注意，不是vignette而是vignettes，否则会识别错误），说明书则存放在这个文件夹下。vignettes文件顾名思义就是手册，需要自己编写，当然可以越详细越好，可以是.Rd或者.Rmd。编写完后将.Rmd文件放到vignettes文件夹里。由于文件题头里声明了文件是pdf或者html，在打包的时候会自动生成。

推荐使用R markdown来编写，可以使用下述代码创建vignettes文件夹，它会在文件夹里创建vignettes模板，名字就是你传入的字符串，比如下述代码传入"my-vignette"，模板名称就是"my-vignette.Rmd"。 模板里已经预先写好了vignettes需要声明的header等信息，自己再修改内容就可以了。怎么写就不详述了，具体可以看[R markdown的使用手册](https://bookdown.org/yihui/rmarkdown/)。

usethis::use_vignette("my-vignette")

还有如果是想写bioconductor的包，他们的vignettes有一个推荐的模板， 具体可以看[BiocStyle](https://bioconductor.org/packages/devel/bioc/vignettes/BiocStyle/inst/doc/AuthoringRmdVignettes.html)。

此外，*-manual.pdf文件里面是把上述所有rd文件整理在一个pdf文件里，可以在build包之后自动生成，不用自己写。笔者原来还以为得自己写呢。

devtools::build_manual(pkg = ".", path = './doc')

### **5、将R包上传到github**

#### **5.1 R包的上传**

将R包发布到github可以方便版本管理和收集反馈。我们可以新建一个本地仓库，然后再把本地仓库远程到github仓库。可以按照以下步骤来执行，可见[参考](http://kbroman.org/pkg_primer/pages/github.html)：

1） 申请一个github账号，并创建一个新的仓库repository，以R包命名.

2）cd到你创建R包的文件夹，然后执行以下命令git初始化你的R包文件夹为本地仓库。当然这需要你先安装git。

git init

然后执行以下命令把R包文件夹里的所有文件添加到仓库中, 并加上注释message。

git add .
git commit -m 'submit'

3）将本地仓库远程到github仓库

git remote add origin https://github.com/usrName/repositoryName

然后将文件传输到远程仓库，作为master：

git push -u origin master

以后每次更新，只需在本地仓库更新后再远程传输一次即可。

另外补充说明，从github上安装R包，也可以使用'devtools'包。

devtools::install_github("usrName/repositoryName")

如果你想查看手册需要build这个包，可以执行：

devtools::install_github('repositoryName/repositoryName', 
  build = TRUE, build_opts = c("--no-resave-data"), force = TRUE)

#### 5.2 R包维护

通常，我们上传github之后，会写一个README.md，我们需要把它第一时间同步到我们的本地库里。

1）查看远程仓库：

 git remote -v

2）把远程库更新到本地

git fetch origin master

3）比较远程更新和本地版本库的差异

git log master.. origin/master

4）合并远程库

git merge origin/master

然后我们一般会在本地仓库进行代码修改（如果是自己维护的话），修改完后将本地库更新

git add .
git commit -m "message"

最后将本地库更新推送到远程库：

git push origin master

查看本地库的状态：

git status