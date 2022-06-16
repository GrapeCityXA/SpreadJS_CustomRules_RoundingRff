# SpreadJS_CustomRules_RoundingRff
在纯前端在线表格中实现自定义规则：修约功能
### SpreadJS 示例，基于 JavaScript组件实现自定义规则：修约

该示例包括使用 SpreadJS API 的演示脚本，可用于实现包含合并单元格的数据绑定。
有关 SpreadJS API 的更多信息，请参阅[SpreadJS API指南]( https://demo.grapecity.com.cn/spreadjs/help/api/) 和[帮助手册]( https://help.grapecity.com.cn/pages/viewpage.action?pageId=5963808)。
 

目录：
-	运行步骤
-	控件初始化
-	示例代码
-	关于 SpreadJS
外部文件：
-	临时授权申请



### 运行步骤
1、在开始之前，请确保您已满足以下先决条件：

要运行 SpreadJS，浏览器必须支持 HTML5，客户端导入和导出 Excel 需要 IE10及以上。请先了解 [SpreadJS 的产品使用环境]( https://www.grapecity.com.cn/developer/spreadjs/selection-guide/product-use-environment)，并申请临时部署授权激活
安装并更新NodeJS和NPM
2、克隆或下载此代码库
3、初始化控件，并运行示例脚本

#### 控件初始化
1、	首先，创建一个新页面，并在页面上输入以下代码：
```
<!DOCTYPE html>
    <html>
    <head>
        <title>Spread HTML test page</title>
```
2、在页面中添加对 Spread.JS 的引用。代码如下。需要注意的是，Spread 提供压缩过
```
（minified）的 JavaScript 文件和和用于调试的文件：
<script src="[Your_Scripts_Path]/gc.spread.sheets.all.xxxx.min.js" type="text/javascript"></script>
```

3、添加 CSS 文件以改变Spread.JS 的外观。默认的CSS文件名为： 
gc.spread.sheets.xxxx.css，里面包含了所有的默认样式。该 CSS 文件将会影响滚动条，筛选框及其子元素，单元格和下方标签栏的样式。引入 CSS 的代码如下：

```
//<link href="[Your_CSS_Path]/gc.spread.sheets.xxxx.css" rel="stylesheet" type="text/css"/>
//OR
<link href="[Your_CSS_Path]/bootstrap/bootstrap.min.css" rel="stylesheet" type="text/css"/>
<link href="[Your_CSS_Path]/bootstrap/bootstrap-theme.min.css" rel="stylesheet" type="text/css"/>
```
4、添加产品授权，代码为：
```
GC.Spread.Sheets.LicenseKey = "xxx";
```
5. 添加控件初始化代码。本例会在一个 id 为“ss”的 DOM 元素上初始化 Spread.Sheets：
```
<script type="text/javascript">
// Add your license
 GC.Spread.Sheets.LicenseKey = "xxx";
// Add your code
 window.onload = function(){
var spread = new GC.Spread.Sheets.Workbook(document.getElementById("ss"),{sheetCount:3});
var sheet = spread.getActiveSheet();
 }
</script>
</head>
<body>
```
6、 创建一个 id 为 “ss”的元素，Spread.Sheets 将在该 DOM 中初始化：
```
<div id="ss" style="height: 500px; width: 800px"></div>
</body>
</html>
```
#### 示例代码
```
HTML：
    <div class="container">
    
        <div class="full-height clearfix mt2">
    
            <div class="inputContainer">
                <p>
                    规则： （1）被修约的数字小于5时，该数字舍去； （2）被修约的数字大于5时，则进位； （3）被修约的数字等于5时，要看5前面的数字，若是奇数则进位，若是偶数则将5舍掉，即修约后末尾数字都成为偶数；若5的后面还有不为“0”的任何数，则此时无论5的前面是奇数还是偶数，均应进位。
                </p>
            </div>
    
    
    
            <div class="col col-11 full-height ">
                <div id="ss" style="height:400px; font-family:simkai"></div>
    
            </div>
        </div>
    </div>
CSS：
    body {
        background: rgb(250, 250, 250);
        color: #333;
        font-family: mtcorsva !important;
    }
    
    #ss {
        border: 1px #ccc solid;
    }
    
    .container {
        width: 95%;
        background: rgb(250, 250, 250);
        margin: 0 auto;
        height: 400px;
    }
    
    .full-height {
        height: 100%;
    }
    
    .left {
        height: 100%;
        overflow: auto;
    }
JavaScript：
     // Title:自定义函数：四舍六入
     // Description：自定义函数：四舍六入
     // Tag:自定义函数，四舍六入
    
    
     var FdaFunction = function() {
         this.name = "FDA";
         this.minArgs = 1;
         this.maxArgs = 2;
     };
     FdaFunction.prototype = new GC.Spread.CalcEngine.Functions.Function();
     FdaFunction.prototype.description = function() {
         return {
             description: "对value进行四舍六入五留双修约，保留小数点后指定位数",
             parameters: [{
                 name: "value",
                 repeatable: false,
                 optional: false
             }, {
                 name: "places",
                 repeatable: false,
                 optional: false
             }]
         }
     }
     FdaFunction.prototype.isContextSensitive = function() {
         return true;
     }
     FdaFunction.prototype.evaluate = function(context, value, places) {
         var context = arguments[0];
         var num;
         if (!isNaN(parseInt(value)) && !isNaN(parseInt(places))) {
    
             var viewport = designer.wrapper.spread.getActiveSheet().getFormatter(context.row, context.column, GC.Spread.Sheets.SheetArea.viewport);
             if (isEmpty(viewport)) {
                 designer.wrapper.spread.getActiveSheet().setFormatter(context.row, context.column, "@", GC.Spread.Sheets.SheetArea.viewport);
             }
             value = NP.strip(value, value.toString().length);
             /*value = NP.strip(value);*/
             if (value < 0) {
                 num = -value;
             } else {
                 num = value;
             }
    
             if (places < 0)
                 return value;
             var v = Number(num);
             var s = v + "";
             var result = "";
             if (s.indexOf(".") != -1) {
                 var s_1 = s.substring(s.indexOf(".") + 1);
                 if (s_1.length == parseInt(places) + 1) {
                     if (s_1.endsWith("5")) {
                         var s_2 = s.substr(0, s.length - 1);
                         var n = Number(s_2);
                         var s_n = n.toFixedELN(places);
                         var x = s_n.substring(s_n.length - 1);
                         if (Number(x) % 2 == 0) {
                             result = s_n;
                         }
                     }
                 }
             }
             if (result == "") {
                 result = v.toFixedELN(places);
             }
    
             if (value < 0) {
                 result = "-" + result;
                 if (result.startsWith("-0")) {
                     if (Number(result) == 0)
                         result = result.substring(1, result.length);
                 }
             }
    
             return result;
         } else {
             return "#VALUE!";
         }
     }
     var spread = new GC.Spread.Sheets.Workbook(document.getElementById("ss"), {
         sheetCount: 2
     });
     var sheet = spread.getActiveSheet();
     sheet.getCell(2, 1).text('自定义单元格：');
     sheet.getCell(2, 1).value(1.432);
     sheet.setColumnWidth(1, 120);
    
     var fda = new FdaFunction();
     sheet.addCustomFunction(fda);
    
     sheet.setFormula(2, 2, "=fda(C1)");
```
#### 关于 SpreadJS
[SpreadJS]( https://www.grapecity.com.cn/developer/spreadjs) 是一款基于 HTML5 的纯前端表格控件，兼容 450 多种 Excel 公式，具备“高性能、跨平台、与 Excel 高度兼容”的产品特性。使用 SpreadJS，可直接在 Angular、 React、 Vue 等前端框架中实现高效的模板设计、在线编辑和数据绑定等功能，为最终用户提供高度类似 Excel 的使用体验。
 

