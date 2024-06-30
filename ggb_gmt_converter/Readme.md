# ggb & gmt  Converter

一个简易的 $\color{#1565c0}{ggb}$ （Geogebra作图文件）与 $\color{#ff7f00}{gmt}$ （Euclidea关卡文件）转换器

暂时不会Geogebra自带的api，所以另外用的python

关于 $\color{#1565c0}{ggb}$ 与 $\color{#ff7f00}{gmt}$ 文件的结构，请参阅 [参考文档](strucmmar.md "参考")

## ggb2gmt

将 $\color{#1565c0}{ggb}$ 文件转为 $\color{#ff7f00}{gmt}$ 文件的python程序

### 作图

考虑到Euclidea的作图工具有限，请在本目录下的“default.ggb”中，按以下要求进行Geogebra的绘图（当然，使用新建的或者现有的 $\color{#1565c0}{ggb}$ 文档也可以，但是同样需要满足这些要求，且可能还要自行导入工具）：

* 为使Euclidea游戏与 $\color{#1565c0}{ggb}$ 中的绘制区域吻合，请尽量将图形控制在**x方向坐标-300到300、y方向坐标-250到250**的范围内，如有放大/缩小的必要，请记住各元素相对于初始绘制范围的位置，以免出现游戏中图形超范围或过小的情况
* 当对象的地位相等时，请按照作图的**先后顺序**构造（如：点C比点B先绘制，那么构造线段应该由C向B），否则会因为标准不一样而出现一些奇怪的现象

基础工具：

* 自由点： 平面内任选
* 对象上的点：在对象上的某处点击（由于 $\color{#1565c0}{ggb}$ 存储该对象的特殊性 ~~本人很懒~~ ，线上的点目前无法在 $\color{#ff7f00}{gmt}$ 中复刻出同样的位置，转换时只能随机生成一个位置参数，所以请参见参考文档gmt vsc扩展的提示，在 $\color{#ff7f00}{gmt}$ 文件中调整）
* 直线：选择两点（有时要考虑选择的顺序）
* 射线：选择顶点和另一点，要考虑顺序
* 线段：选择两点（有时要考虑选择的顺序）
* 交点：选择两线、线圆或圆圆 （圆与直线/圆的交点会有参数决定是哪一个，这个参数系统在两方有一定区别，尤其是 $\color{#ff7f00}{gmt}$ 中有一个类似于引导选择的参数，即若已经有一交点，那这个参数可以避免另一交点与其重合，且无视控制是哪个的参数，而 $\color{#1565c0}{ggb}$ 中没有（其实是一律写为2），所以如果程序给出的参数有误，请自行修改，具体方法参见参考文档或gmt vsc扩展）
* 圆：选择圆心与半径

高级工具（特别注意那些在 $\color{#ff7f00}{gmt}$ 需要转换的，当然，**不建议使用**这些 $\color{#ff7f00}{gmt}$ 不支持的功能，否则可能会出一些奇怪的错误）：

* 中点/中心：选择两点或一圆，**如果选择线段， $\color{#ff7f00}{\textbf{\textit{gmt}}}$ 中处理为线段两端点** *（事实上，两点与一圆的构造指令是不同的）*
* 垂线：选择一点和一线（选择顺序不重要， $\color{#1565c0}{ggb}$ 内部会调整）
* 平行线：选择一点和一线（选择顺序不重要， $\color{#1565c0}{ggb}$ 内部会调整）
* 中垂线：选择两点，**如果选择线段， $\color{#ff7f00}{\textbf{\textit{gmt}}}$ 中处理为线段两端点** （有时要考虑选择的顺序）
* 角平分线：选择三点，其中第二点是角的顶点，**不能选择两条线因为 $\color{#ff7f00}{\textbf{\textit{gmt}}}$ 不支持** （第一，三点有时要考虑选择的顺序）
* 圆规：选择两点 （确定的半径），再选择圆心，**如果选择线段， $\color{#ff7f00}{\textbf{\textit{gmt}}}$ 中处理为线段两端点；如果选择圆， $\color{#ff7f00}{\textbf{\textit{gmt}}}$ 中则处理为圆心+圆上一点，如果找不到现成的点会重新定义一个** （前两点有时要考虑选择的顺序）
* 三点圆：选择三点，**为后续处理方便， $\color{#ff7f00}{\textbf{\textit{gmt}}}$ 中自动生成中心但不显示** （有时要考虑选择的顺逆时针顺序）
* 切线：选择一点和一圆，会同时生成两条线，无论是否隐藏都会转换进 $\color{#ff7f00}{gmt}$ ，**如果选择线和圆， $\color{#ff7f00}{\textbf{\textit{gmt}}}$ 中处理为过圆心垂线+两交点+作切** （选择顺序不重要， $\color{#1565c0}{ggb}$ 内部会调整）
* 极线：选择一点和一圆，**如果选择线和圆， $\color{#ff7f00}{\textbf{\textit{gmt}}}$ 中处理为过圆心的垂线** （选择顺序不重要， $\color{#1565c0}{ggb}$ 内部会调整；以及后者构造指令不一样）

自制工具（ $\color{#ff7f00}{gmt}$ 中能直接使用而 $\color{#1565c0}{ggb}$ 不能的，故打包，源代码文件在同目录下的ggb_src文件夹。使用不熟练者，可以多试几次以熟悉）：

* 极点：选择一线和一圆（ $\color{#1565c0}{ggb}$ 会强制自制工具的选择顺序，下同）
* 无穷远点：选择一直线或射线，会同时生成两个假想的点，对其的操作可以在“代数区”进行
* 定值角：选择顶点和另一点，然后输入角度（**请选择逆时针**），生成的是射线 *（注意先选的是顶点，和其他几个不同）*
* 复制角：和角平分线工具一样选择三点，再选择新的一点和一顶点，生成的也是射线
* 平移线段：选择两点 *（代表一条线段）*，再选择一点，**生成的为含端点的反向延长的线段，但是在 $\color{#ff7f00}{\textbf{\textit{gmt}}}$ 中，端点是用圆规+交点定义的** *（前两点有时要考虑选择的顺序）*

### 设定

Euclidea是关卡式的，所以有关卡的设置（即初始条件，所求作，探索模式，移动模式，规则等，具体语法见参考文档）

在 $\color{#1565c0}{ggb}$ 中，可以使用list（列表）来确定上述设定的具体对象，该程序则通过识别列表中的内容来转换

* “named”设定会对名称超过1字符的对象重新命名，如果（一般是线和圆）出现这种情况，请在 $\color{#ff7f00}{gmt}$ 文件中自行调整
* 由于 $\color{#ff7f00}{gmt}$ 文件的复杂性，“**rules**”目前无法在 $\color{#1565c0}{ggb}$ 中设定，如有需要，请在转换后的 $\color{#ff7f00}{gmt}$ 文件中自行添加
* 对象名称对照表会在终端输出，可以进行对照（当然也可以直接硬读 $\color{#ff7f00}{gmt}$ ）

**操作方法：**

* 列表操作方法
  * 使用工具栏中的“列表”工具，右键框选一个矩形，其中的对象将会被放入一个新列表
    * 这种方法至少要框选2个对象，并且框选不了直线，列表名也是默认的，需要后期手动调整
  * 直接在指令栏中创建新列表（如包含A、B、c的列表l1则输入“l1={A, B, c}”回车）
    * 这种方法可以自定义名字，但是不能手选对象，需要记住对象的名称
  * 双击代数区中已经创建的列表，进行修改（**推荐**）
    * 可以点击选择代数区或者绘图区的对象，相对更方便
    * 可以先随意创建一个列表，然后再按此方法操作
* 依次创建下列列表，并将相应的对象选入：
  * “initial”
  * “named”（如果需要则写）
  * “movepoints”（如果需要则写）
  * “result”（如果有多个就在后面加数字，如“result1”“result2”等）
  * “display”（即result冒号后的内容，如果需要则写；如果有多个就在后面加数字，如“display1”“display2”等，但需要对应）
  * “explore”

### 转换

将 $\color{#1565c0}{ggb}$ 文件与py文件放于同一目录（目录下只能有一个待转换的 $\color{#1565c0}{ggb}$ 文件），运行py程序，便可得到一个 $\color{#ff7f00}{gmt}$ 文件

#### 其他

如果转换后 $\color{#ff7f00}{gmt}$ 中出现了错误（可能会在终端提示，或转换成一条注释），请仔细检查 $\color{#1565c0}{ggb}$ 的绘制是否完全按照以上要求进行

## gmt2ggb

将 $\color{#ff7f00}{gmt}$ 文件转换成 $\color{#1565c0}{ggb}$ 文件的python程序

有关 $\color{#ff7f00}{gmt}$ 的语法结构，已经在参考文档中详细说明。如果要编辑 $\color{#ff7f00}{gmt}$ 文件，可以按其中的规则或以 $\color{#ff7f00}{gmt}$  vsc扩展为辅助编写

### 使用方法

须知：

 $\color{#ff7f00}{gmt}$ 文件的游戏设定中的initial、named、movepoints、result、explore部分会转换成可见对象，其余则隐藏