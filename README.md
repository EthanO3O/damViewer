# **DamViewer 接口文档**
## **版本历史**
| 日期 | 版本号 | 作者 | 备注 |
| ------------ | ----------- | ----------- | ----------- |
| 2023-11 | 1.1.0 | Ethan | 较早先版本改动较大,废弃部分多余方法(见最后),修改大部分方法参数,增加了一些功能 |
| 2023-11 | 1.1.3 | Ethan | 修复外部容器scale时选取模式的位置错误问题,增加实例化时待选参数 |
| 2023-11 | 1.1.4 | Ethan | 修复组件销毁时可能存在的错误 |
| 2023-12 | 1.1.5 | Ethan | 移除加载模型后自动调整摄像机,如有需要手动调用 |
| 2023-12 | 1.1.6 | Ethan | 切片测点加载后不再自动显示,切片在不可见时,setdata依然生效 |
| 2024-3 | 1.1.9 | Ethan | 增加轴对换,增加视角字符 |
| 2024-3 | 1.2.0 | Ethan | 增加容器切换 |
| 2024-3 | 1.2.2 | Ethan | 增加缝开度与屈服解析 |
| 2024-4 | 1.2.3 | Ethan | 增加绝对最大值最小值获取方法 |
---
- # 目录
  - [引入&实例化](#引入实例化)
    - [引入](#引入)
    - [实例化](#实例化)
  - [文件加载](#文件加载)
    - [加载模型文件vtm](#加载模型文件vtm)
    - [加载数据文件vtr](#加载数据文件vtr)
    - [加载切片文件cut](#加载切片文件cut)
    - [加载测点](#加载测点)
    - [加载多个数据vtr文件](#加载多个数据vtr文件)
    - [加载多个obj文件](#加载多个obj文件)
  - [已加载文件信息获取](#已加载文件信息获取)
    - [获取坝段编号信息](#获取坝段编号信息)
    - [获取vtr中步骤信息](#获取vtr中步骤信息)
    - [获取数据文件中包含数据种类信息](#获取数据文件中包含数据种类信息)
    - [获取切片编号信息](#获取切片编号信息)
    - [获取数据文件中包含数据种类信息](#获取数据文件中包含数据种类信息)
    - [获取摄像机当前位置信息](#获取摄像机当前位置信息)
  - [数据&颜色](#数据颜色)
    - [切换已加载坝段&切片的数据](#切换已加载坝段切片的数据)
    - [多数据vtr文件加载后切换数据](#多数据vtr文件加载后切换数据)
    - [设置当前选中数据的最大最小值](#设置当前选中数据的最大最小值)
    - [重置当前选中数据的最大最小值](#重置当前选中数据的最大最小值)
    - [获取已加载坝段的绝对最大最小值](#获取已加载坝段的绝对最大最小值)
  - [显示&隐藏](#显示隐藏)
    - [显示&隐藏坝段/切片/测点](#显示隐藏-坝段切片测点)
  - [显示风格切换](#显示风格切换)
    - [设置背景色](#设置背景色)
    - [设置超出阈值颜色](#设置超出阈值颜色)
    - [设置坝段/切片是否显示网格](#设置坝段切片是否显示网格)
    - [设置坝段/切片是否发光](#设置坝段切片是否发光)
    - [设置坝段/切片显示模式](#设置坝段切片显示模式)
    - [设置坝段/切片透明度](#设置坝段切片透明度)
    - [显示/隐藏-坝段边框](#显示隐藏-坝段边框)
    - [切换色板颜色](#切换色板颜色)
  - [坝段&切片变形](#坝段切片变形)
    - [显示变形](#显示变形)
    - [关闭变形](#关闭变形)
  - [鼠标选取模式](#鼠标选取模式)
    - [开启选取模式](#开启选取模式)
    - [关闭选取模式](#关闭选取模式)
  - [摄像机控制](#摄像机控制)
    - [设置摄像机位置](#设置摄像机位置)
    - [设置正交/透视摄像机](#设置正交透视摄像机)
  - [其他设置](#其他设置)
    - [设置轴对换](#设置轴对换)
    - [设置显示容器](#设置显示容器)
  - [卸载&释放内存](#卸载释放内存)
    - [卸载组件](#卸载组件)
  - [废弃函数列表](#废弃函数列表)
---
---

## *引入&实例化*

> ### **引入：**
> ```javascript 
> import './damviewer.js'
> ```

> ### **实例化：**
> - **方法名：**
> **[new DamViewer(config)](#)**
> - **参数config说明**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |
>| HTMLElement | dom | 否 | - | - |
>| bool | colorPannal | 是 | false |是否使用默认色板 |
>| bool | axesBox | 是 | false |是否启用轴向盒 |
>| bool | axisSwap | 是 | false |是否启用轴向对调 |
>| number | pointSize | 是 | 0.5|ray模式下,跟随鼠标的小球大小|
>| number array | selectColor | 是 | [1,1,1]|ray模式下,被选中测点要改变的颜色,表示(r,g,b)|
>| number | scaleW | 是 | 1 |如果上级页面css中有transfrom scaleX 填入该值|
>| number | scaleH | 是 | 1 |如果上级页面css中有transfrom scaleY 填入该值|
>- **代码示例：**
>```javascript
> const dom = document.getElementById('vtk-renderer')
> let config = {
>   container:dom,
>   colorPannal:false,
>   axesBox:false,
>   axisSwap:false,
>   pointSize:0.5,
>   selectColor:[1,0,0],
>   scaleW:0.5,
>   scaleH:0.5,
> }
> const viewer = new DamViewer(config)
>```
---
[返回目录](#目录)

---
## *文件加载*

> ### **加载模型文件vtm：**
>- **方法名：**
> **[readVtm(path)](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| string | path | 否 | - | promise | 要读取文件的路径 |
>- **代码示例：**
>```javascript
> viewer.readVtm('./test.vtm').then(()=>{
>    //操作坝段相关
>   
> })
>```

> ### **加载数据文件vtr：**
>- **方法名：**
> **[readVtr(path)](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| string | path | 否 | - | promise | 要读取文件的路径 |
>- **代码示例：**
>```javascript
> viewer.readVtr('./test.vtr').then(()=>{
>    //操作坝段数据相关
> })
>```

> ### **加载测点：**
>- **方法名：**
> **[buildMPoint(json,colorConfig)](#)**
>  - **参数说明**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| json | json | 否 | - | - | 测点json数据 |
>| object | colorConfig | 否 | - | - | 颜色配置对象 |
>  - **参数colorConfig说明**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| number | size | 是 | 0.5 | - | 测点模型尺寸 |
>| object | colors | 是 | 白色 | - | 测点数据对象数组,详情见代码注释 |
>- **代码示例：**
>```javascript
> let colorConfig={
>    size:0.4,
>    colors:{//types和project请二选一,此处只处理颜色分类
>        // types:[
>        //     {key:'渗流监测',color:[0,1,1]},
>        //     {key:'变形监测',color:[1,1,0]},
>        // ],
>        project:[
>            {key:'扬压力',color:[0,1,1]},
>            {key:'侧向绕渗',color:[0,0.5,1]},
>            {key:'垂直位移',color:[1,1,0]},
>            {key:'水平位移',color:[1,0.5,0]},
>        ]
>    }
>}
>viewer.buildMPoint(json,colorConfig)
>//加载完成,使用showSome 控制隐藏/显示
>```

> ### **加载切片文件cut：**
>- **方法名：**
> **[readCut(path)](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| string | path | 否 | - | promise | 要读取文件的路径 |
>- **代码示例：**
>```javascript
> viewer.readCut('./test.cut').then(()=>{
>    //操作切片展示情况
> })
>```

> ### **加载多个数据vtr文件：**
>- **方法名：**
> **[preloadVtr(pathArr)](#)**
> - **参数说明**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| string array | pathArr | 否 | - | promise数组 | 要读取文件的路径 |
>- **代码示例：**
>```javascript
> let items = viewer.preloadVtr(['./test1.vtr','./test2.vtr','./test3.vtr'])
> Promise.all(items).then(()=>{
>   //播放切换动画,定时器请自行实现
> })
>```

> ### **加载多个obj文件：**
>- **方法名：**
> **[preloadObj(pathArr)](#)**
> - **参数说明**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| string array | pathArr | 否 | - | promise数组 | 要读取文件的路径 |
>- **代码示例：**
>```javascript
> let items = viewer.preloadObj(['./test1.obj','./test2.obj','./test3.obj'])
> Promise.all(items).then(()=>{
>   //播放切换动画,定时器请自行实现
> })
>```
---
[返回目录](#目录)

---
## *已加载文件信息获取*
> ### **获取坝段编号信息：**
>- **方法名：**
> **[getSections()](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| - | - | 否 | - | 坝段编号数组 | - |
>- **代码示例：**
>```javascript
> viewer.getSections()
> //[1,2,3,4,5,6,....]
>```

> ### **获取vtr中步骤信息：**
>- **方法名：**
> **[getSteps()](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| - | - | 否 | - | 坝段步骤数组 | - |
>- **代码示例：**
>```javascript
> viewer.getSteps()
>//[0,1]  通常只有一步 0
>```

> ### **获取数据文件中包含数据种类信息：**
>- **方法名：**
> **[getDataTypes()](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| - | - | 否 | - | 数据种类对象(字符串) | - |
>- **代码示例：**
>```javascript
> viewer.getDataTypes()
>//{temperature: true, stress: true, displacements: true}
>```

> ### **获取切片编号信息：**
>- **方法名：**
> **[getCutSections()](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| - | - | 否 | - | 切片编号数组 | 切片包含数据与坝段一致 |
>- **代码示例：**
>```javascript
> viewer.getCutSections()
> //[1,2,3,4,5,6,....]
>```

> ### **获取数据文件中包含数据种类信息：**
>- **方法名：**
> **[getDataTypes()](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| - | - | 否 | - | 数据种类对象(字符串) | - |
>- **代码示例：**
>```javascript
> viewer.getDataTypes()
>//{temperature: true, stress: true, displacements: true}
>```

> ### **获取摄像机当前位置信息：**
>- **方法名：**
> **[getCameraPosition()](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| - | - | - | - | 摄像机位置和聚焦对象 | - |
>- **代码示例：**
>```javascript
> let camData = viewer.getCameraPosition()
>//{
>//    "position": [
>//        -15.303015019237016,
>//        -93.97463502186763,
>//        74.3975742915398
>//    ],
>//    "target": [
>//        37.31200256347657,
>//        -10.342500000000001,
>//        -17.099999999999998
>//    ]
>//}
>```
---
[返回目录](#目录)

---
## *数据&颜色*
> ### **切换已加载坝段&切片的数据：**
>- **方法名：**
> **[setData(datatype,axis,step)](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| string | datatype | 否 | - | - | 可选项包含:"temperature","stress","displacements","seepage","opening","yeildzone" |
>| string |axis | 否 | - | - | 见示例中注释 |
>| number | step | 否 | - | - | - |
>| - | - | - | - | 图例最小值到最大值数组 | - |
>- **代码示例：**
>```javascript
> //step填写需要显示的计算步数,可以用 getSteps() 获取当前包含的计算步数
> let step = viewer.getSteps()
>
> //获取包含的数据类型,使用indexOf判断
> let dataTypes = viewer.getDataTypes()
> let dataType ='temperature'
> if(dataTypes.indexOf('temperature')!==-1){
>    viewer.setData('temperature','',step[0])
>  }
> let dataType ='stress'
> if(dataTypes.indexOf('stress')!==-1){
>   let numArr =  viewer.setData('stress','X',step[0])
>   // console.log(numArr)  
>   //['-0.051', '-0.041', '-0.031', '-0.021', '-0.011', '-0.001', '0.009', '0.02', '0.03', '0.04', '0.05']
> }
> // datatype类型有:"temperature","stress","displacements","seepage","opening","yeildzone"
> // 当类型是temperature/seepage/opening/yeildzone时,第二个参数axis请留空:""
> // 当类型是stress时,axis类型:"X","Y","Z","XY","XZ","YZ","Ci","Cii","Ciii"
> // 当类型是displacements时,axis类型:"X","Y","Z","C"
>```

> ### **多数据vtr文件加载后切换数据：**
>- **方法名：**
> **[jumpToFile(args,cb)](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| object | args | 否 | - | - | 重置最大最小值到当前默认值 |
>| function | cb | 否 | - | - | 重置最大最小值到当前默认值 |
>  - **args**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| string | datatype | 否 | 0.5 | - | "temperature","stress","displacements","seepage","opening","yeildzone" |
>| string | axis | 否 | - | - | 同setData的 axis|
>| number | index | 否 | - | - | 要切换到的文件下标,顺序与preloadvtr时相同 |
>| number | num | 否 | - | - | 是否同时显示变形,0表示不变形,同showAmplification |
>| bool | havePit | 是 | - | - | 冲坑模型(仅万家寨) |
>| - | - | - | - | - | cb带回的参数是一个对象,其中nums是图例最小值到最大值数组|
>- **代码示例：**
>```javascript
> viewer.jumpToFile(
>    {
>        datatype:'temperature',
>        axis:'',
>        index:'2',//第三个传入的文件,如果有
>        num:'2',
>    },(res)=>{
>        console.log(res)
>        //{nums: Array(11), index: 2, file: 'nsbd4'}
>    }
> )
>   // console.log(numArr)  
>   //['-0.051', '-0.041', '-0.031', '-0.021', '-0.011', '-0.001', '0.009', '0.02', '0.03', '0.04', '0.05']
>```

> ### **设置当前选中数据的最大最小值：**
>- **方法名：**
> **[setMinMax(min,max)](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| number | min | 否 | - | - | - |
>| number | max | 否 | - | - | - |
>| - | - | - | - | 图例最小值到最大值数组 | 当前数据如果超出设置阈值,则会显示白色,该颜色可使用setExColor方法修改 |
>- **代码示例：**
>```javascript
> let numarr = viewer.setMinMax(0,10)
>   // console.log(numArr)  
>   //['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', '10']
>```

> ### **重置当前选中数据的最大最小值：**
>- **方法名：**
> **[resetMinMax()](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| - | - | - | - | 图例最小值到最大值数组 | 重置最大最小值到当前默认值 |
>- **代码示例：**
>```javascript
> let numarr = viewer.resetMinMax()
>   // console.log(numArr)  
>   //['-0.051', '-0.041', '-0.031', '-0.021', '-0.011', '-0.001', '0.009', '0.02', '0.03', '0.04', '0.05']
>```

> ### **获取已加载坝段的绝对最大最小值：**
>- **方法名：**
> **[updateAbsRange(datatype,axis,step)](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| string | datatype | 否 | - | - | 可选项包含:"temperature","stress","displacements","seepage","opening","yeildzone" |
>| string | axis | 否 | - | - | 见示例中注释 |
>| number | step | 否 | - | - | - |
>| - | - | - | - | 绝对最大值最小值对象与所在材料号 | - |
>- **代码示例：**
>```javascript
> let absRange = viewer.updateAbsRange('stress','Ciii',0)
>   // console.log(absRange)  
>   //{maxSection: '16', minSection: '16', max: 0.058594111, min: 0}
>```
---
[返回目录](#目录)

---
## *显示&隐藏*
> ### **显示&隐藏 坝段/切片/测点：**
>- **方法名：**
> **[showSome(type,numArr,update,resetCam)](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| string | type | 否 | - | -| 有坝段'section',切片'cut',测点'mPoint'三个类型 |
>| array | numArr | 是 | 隐藏选中所有该类型模型 | - | 为有坝段'section',切片'cut'时,填坝段 切片编号,为测点'mPoint'时,填入所需类型文字,如['垂直位移','水平位移'] 或者 ['扬压力','侧向绕渗',...]|
>| bool | update | 是 | 否 | -| 是否更新最大最小值到显示内容 |
>| bool | resetCam | 是 | 否 | -| - |
>| - | - | - | - |图例最小值到最大值数组| - |
>- **代码示例：**
>```javascript
> //隐藏所有section
> let numarr = viewer.showSome('section')
>   // console.log(numarr)  
>   //['-0.051', '-0.041', '-0.031', '-0.021', '-0.011', '-0.001', '0.009', '0.02', '0.03', '0.04', '0.05']
>
> //显示 1 2 3 号section 且更新最大最小值,不更新视距
> let numarr2 = viewer.showSome('section',[1,2,3],true,false)
>   // console.log(numarr2)  
>   //['-0.051', '-0.041', '-0.031', '-0.021', '-0.011', '-0.001', '0.009', '0.02', '0.03', '0.04', '0.05']
>
> //隐藏所有cut,不更新最大最小值,不更新视距
> let numarr3 = viewer.showSome('cut')
>   // console.log(numarr3)  
>   //['-0.051', '-0.041', '-0.031', '-0.021', '-0.011', '-0.001', '0.009', '0.02', '0.03', '0.04', '0.05']
>
> //显示 1 2 号 cut  不更新最大最小值,更新视距
> let numarr3 = viewer.showSome('cut',[1,2],false,true)
>   // console.log(numarr3)  
>   //['-0.051', '-0.041', '-0.031', '-0.021', '-0.011', '-0.001', '0.009', '0.02', '0.03', '0.04', '0.05']
>
> //显示 垂直位移的 测点  不更新最大最小值,不更新视距
> let numarr4 = viewer.showSome('mPoint',['垂直位移'],false,false)
>   // console.log(numarr4)  
>   //['-0.051', '-0.041', '-0.031', '-0.021', '-0.011', '-0.001', '0.009', '0.02', '0.03', '0.04', '0.05']
>```
---
[返回目录](#目录)

---
## *显示风格切换*
> ### **设置背景色：**
>- **方法名：**
> **[setBgColor(r,g,b,a)](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| number(0-1) | r | 否 | - | - | - |
>| number(0-1) | g | 否 | - | - | - |
>| number(0-1) | b | 否 | - | - | - |
>| number(0-1) | a | 否 | - | - | - |
>- **代码示例：**
>```javascript
> //设置背景色为白色
> viewer.setBgColor(1,1,1,1)
> //设置背景色为透明
> viewer.setBgColor(1,1,1,0)
>```

> ### **设置超出阈值颜色：**
>- **方法名：**
> **[setExColor(r,g,b)](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| number(0-1) | r | 否 | 1 | - | - |
>| number(0-1) | g | 否 | 1 | - | - |
>| number(0-1) | b | 否 | 1 | - | - |
>- **代码示例：**
>```javascript
> //设置超出阈值颜色为红色
> viewer.setExColor(1,0,0)
>```

> ### **设置坝段/切片是否显示网格：**
>- **方法名：**
> **[setGrid(type,bool)](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| string | type | 否 | - | - | 可选类型:坝段'section',切片'cut' |
>| bool | bool | 否 | - | - | - |
>- **代码示例：**
>```javascript
> //显示坝段网格
> viewer.setGrid('section', true )
> //隐藏切片网格
> viewer.setGrid('cut', false )
>```

> ### **设置坝段/切片是否发光：**
>- **方法名：**
> **[setLight(type,bool)](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| string | type | 否 | - | - | 可选类型:坝段'section',切片'cut' |
>| bool | bool | 否 | - | - | - |
>- **代码示例：**
>```javascript
> //设置坝段发光
> viewer.setLight('section', true )
> //设置切片不发光
> viewer.setLight('cut', false )
>```

> ### **设置坝段/切片显示模式：**
>- **方法名：**
> **[setDisplayMode(type,num)](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| string | type | 否 | - | - | 可选类型:坝段'section',切片'cut' |
>| number(0,1,2) | num | 否 | 2 | - | 0:点,1:线,2:面 |
>- **代码示例：**
>```javascript
> //设置坝段线模式
> viewer.setDisplayMode('section', 1 )
> //设置切片点模式
> viewer.setDisplayMode('cut', 0 )
>```

> ### **设置坝段/切片透明度：**
>- **方法名：**
> **[setOpacity(type,num)](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| string | type | 否 | - | - | 可选类型:坝段'section',切片'cut' |
>| number(0-1) | num | 否 | - | - | 1为完全不透明,0为透明 |
>- **代码示例：**
>```javascript
> //设置坝段不透明
> viewer.setOpacity('section', 1 )
> //设置切片完全透明
> viewer.setOpacity('cut', 0 )
>```

> ### **显示/隐藏 坝段边框：**
>- **方法名：**
> **[setBorder(bool)](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| bool | bool | 否 | true | - | - |
>- **代码示例：**
>```javascript
> //显示坝段边框
> viewer.setBorder(true)
> //隐藏坝段边框
> viewer.setBorder(false)
>```

> ### **切换色板颜色：**
>- **方法名：**
> **[setColorPannal(num)](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| number(0-186)) | num | 否 | 0 | - | 部分色板对不同模型会显示不了,可以多尝试.如需获取色板图片,可以在实例化时将colorPannal设置为true后截图使用 |
>- **代码示例：**
>```javascript
> //使用默认色板
> viewer.setColorPannal(0)
> //使用浅色彩虹(海河)
> viewer.setColorPannal(1)
>```
---
[返回目录](#目录)

---
## *坝段&切片变形*
> ### **显示变形：**
>- **方法名：**
> **[showAmplification(num)](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| number(0-3) | num | 否 | - | - | 0为不变形,不要输入过大数字建议在3以内 |
>- **代码示例：**
>```javascript
> //设置变形系数1
> viewer.showAmplification(1)
>```

> ### **关闭变形：**
>- **方法名：**
> **[hideAmplification()](#)**
> 
>- **代码示例：**
>```javascript
> //取消变形
> viewer.hideAmplification()//
>```
---
[返回目录](#目录)

---
## *鼠标选取模式*
> ### **开启选取模式：**
>- **方法名：**
> **[startRay(target,cb)](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| string | target | 否 | - | - | 可选项有:坝段'section',切片'cut',测点'mPoint' |
>| function | cb | 否 | - | - | 回调函数,用来接收所选对象数据 |
>- **代码示例：**
>```javascript
> //拾取坝段
> viewer.startRay('section',(data)=>{
>    console.log(data)
>    //{section: '1', data: 0.00124956}
>})
> //拾取切片
> viewer.startRay('cut',(data)=>{
>    console.log(data)
>    //{section: '1', data: 0.00124956}
>})
> //拾取测点
> viewer.startRay('mPoint',(data)=>{
>    console.log(data)
>    //{"name": "G-TP2","type": "变形监测","project": "水平位移"}
>})
>```

> ### **关闭选取模式：**
>- **方法名：**
> **[stopRay()](#)**
> 
>- **代码示例：**
>```javascript
> //关闭选取模式
> viewer.stopRay()
>```
---
[返回目录](#目录)

---
## *摄像机控制*
> ### **设置摄像机位置：**
>- **方法名：**
> **[resetCamera(direction,dis,pos,target)](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| string | direction | 否 | - | - | 可选项有:"X","-X","Y","-Y","Z","-Z","custom","top","bottom","left","right","front","back",不填 |
>| number | dis | 是 | - | - | 摄像机距离,默认计算可填写 false 或 0 |
>| number Array | pos | 是 | - | - | 摄像机位置数组 |
>| number Array | target | 是 | - | - | 摄像机聚焦位置数组 |
>- **代码示例：**
>```javascript
> //从x正方向查看模型
> viewer.resetCamera('X')
> //从Y负方向查看模型
> viewer.resetCamera('-Y')
> //自定义摄像机查看位置,相机放置坐标xyz 20,20,20 聚焦到xyz 0,0,0
> viewer.resetCamera('custom',false,[20,20,20],[0,0,0])
>```

> ### **设置正交/透视摄像机：**
>- **方法名：**
> **[setCamMode(bool)](#)**
> 
>| 参数类型 | 参数名称 | 可选 |默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| bool | bool | 否 | false | - | true为正交相机, false为透视相机 |
>- **代码示例：**
>```javascript
> //正交
> viewer.setCamMode(true)
> //透视
> viewer.setCamMode(false)
>```
---
[返回目录](#目录)

---
## *其他设置*
> ### **设置轴对换：**
>- **方法名：**
> **[setSwap(bool)](#)**
> 
>| 参数类型 | 参数名称 | 可选 | 默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| bool | bool | 否 | - | - | 需要在加载模型前设置 |

>- **代码示例：**
>```javascript
> //对换横轴和深度轴
> viewer.setSwap(true)
>```

> ### **设置显示容器：**
>- **方法名：**
> **[setContainer(dom,[width,height])](#)**
> 
>| 参数类型 | 参数名称 | 可选 | 默认值 | 返回值 | 备注 |
>| :-------- | :------ |------ | ------ |------ |------ |
>| dom | htmlElement | 否 | - | - | 设置需要放置的容器 |
>| width | number | 是 | - | - | 容器宽度 |
>| height | number | 是 | - | - | 容器高度 |

>- **代码示例：**
>```javascript
> //从当前div切换到rongqi div
> viewer.setContainer(document.getElementById('rongqi'))
>```

---
[返回目录](#目录)

---
## *卸载&释放内存*
> ### **卸载组件：**
>- **方法名：**
> **[destroy()](#)**
>- **代码示例：**
>```javascript
> //销毁组件时调用
> viewer.destroy()
>```
---
[返回目录](#目录)

---
## *废弃函数列表*
>```javascript
> //销毁组件时调用
> viewer.pause()
> viewer.stop()
> viewer.setMax()
> viewer.setMin()
> viewer.hideSections()
> viewer.showSections()
> viewer.showSomeSections()
> viewer.toggleGrid()
> viewer.toggleLight()
>```
