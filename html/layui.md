# Layui

 `Layui` 经典模块化前端 UI 框架

> [layuiAdmin pro v1.x 开发者文档](https://noddingfish.github.io/gitbook-technical-note/html/layui/layuiadmin.html)



## 组件

### 1、formSelects 4.x 多选下拉框

> [formSelects-v4.js 基于Layui的多选解决方案](https://hnzzmsf.github.io/example/example_v4.html)



### 2、Dtree 树形组件

> [dtree 数据树文档 - layui.dtree](http://www.wisdomelon.com/DTreeHelper)



## 常见问题

### 1、如果 `layui` 数据表格列宽自适应出现横向滚动条的解决方案：

正常情况下，自适应的列宽不会导致 table 容器出现滚动条。如果你的出现了，请按照下述方案解决：

1. 如果 table 是在 layui 的后台布局容器中（注意：不是在 iframe 中），在你的页面加上这段 CSS：

   ```css
   .layui-body{overflow-y: scroll;}
   ```

2. 如果 table 是在独立的页面，在你的页面加上这段 CSS：

   ```css
   body{overflow-y: scroll;}
   ```

总体而言，table 列宽自适应出现横向滚动条的几率一般是比较小的，主要原因是 table 的渲染有时会在浏览器纵向滚动条出现之前渲染完毕，这时 table 容器会被强制减少滚动条宽度的差（一般是 17px），导致 table 的横向滚动条出现。建议强制给你的页面显示出纵向滚动条。

若还不能解决：

```css
.layui-table-body{overflow-x: hidden;}
```

> [如果 layui 数据表格列宽自适应出现横向滚动条的解决方案](<https://fly.layui.com/jie/18737/>)



### 2、`Layui` 第三方组件： `formSelects 4.x`

引入后需要渲染：

```js
formSelects.render();
```

> [formSelects-v4.js 基于Layui的多选解决方案](https://hnzzmsf.github.io/example/example_v4.html)



### 3、`Layui` 中弹出层的 `btn` 提交 `form` 表单；

###### 思路：

<u>在 `form` 表单中设置隐藏的 `button` ，用弹出层中的提交按钮 `click` 隐藏的 `button`。</u>

###### 在弹出层的 `form` 表单中加入一下 `html` ，隐藏：

```html
<button class="layui-btn layui-btn-normal layui-col-md-offset10 menuSubmit" style="display: none;" lay-filter="menuSubmit" lay-submit>提交</button>
```

###### `js` 中可以这么写：

```js
admin.popup({
    id: 'layor-popup-order-single'
    , title: '编辑菜单'
    , anim: -1
    , area: ['50%', '60%']
    , shade: [0.5, '#000000']
    , skin: 'layui-anim layui-anim-upbit layor-body'
    , btn: ['提交']
    , success: function () {
        view(this.id).render('auth/menu/editView', node).then(function () {

        }).done(function () {
            //页面加载后的回调 do something
            form.render();//渲染页面
        })
    },
    yes: function (index, layero) {
        layero.find('.menuSubmit').click();//点击隐藏的按钮
    }
});
```

> 参考：[layer弹出层里的里form如何监听事件在弹出层的按钮上?](https://fly.layui.com/jie/5581)



### 4、表格中使用下拉选择框

增加的 `css` 样式：

```css
<style>
    /* 防止下拉框的下拉列表被隐藏---必须设置--- 此样式和表格的样式有冲突 如果表格列数太多 会出现错乱的情况 目前我的解决方法是忽略下拉框的美化渲染 <select lay-ignore> */
    .layui-table-cell {
        overflow: visible;
    }

    .layui-table-box {
        overflow: visible;
    }

    .layui-table-body {
        overflow: visible;
    }
    /* 设置下拉框的高度与表格单元相同 */
    td .layui-form-select{
        margin-top: -10px;
        margin-left: -15px;
        margin-right: -15px;
    }
</style>
```

效果如图所示：

![1562135097510](D:\Gitbook\docsify-technical-note\images\1562135097510.png)



### 5、`Layui` 阻止冒泡

`html` 增加点击事件阻止冒泡，复选框需要注意：

```html
<!-- 复选框阻止冒泡增加一个 li -->
<li class="stopBubble layui-input-inline">
	<input type="checkbox" name="" lay-filter="TEST" lay-skin="primary">
</li>
<!-- 其他的冒泡增加一个点击事件阻止 -->
<input type="number" class="layui-input goodsNum stopBubble">
```

```js
//TODO 阻止冒泡（checkbox & input）
$(".stopBubble").off('click').on('click', function (that) {
    layui.stope(that);//阻止冒泡
});
```



### 6、 `Layui` 中 `checkbox` ，`form.render()` 问题

> 参考：https://www.jianshu.com/p/370d2a18fd91

下面的方法，无法更新渲染

```js
$(this).attr('checked',true);
form.render('checkbox');
```

需要改为：

```js
$(this).prop('checked',true);
form.render('checkbox');
```

根据官方的建议：

​	具有 `true` 和 `false` 两个属性的属性，如 `checked`, `selected` 或者 `disabled` 使用 `prop()` ，其他的使用 `attr()`
