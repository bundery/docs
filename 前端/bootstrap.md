# bootStrap

## 栅格系统

栅格系统是通过 `row` 和 `column` 组合来创建页面布局的。row 必须包含在 `.container`（固定宽度）或者 `.container-fluid` （自适应）中，列都要包含在在 row 内，一行最多占 **12** 个列，如果超过了12 列多余的列将被作为一个整体另起一行排列。

- col-lg 用于大屏设备 (min-width:1200px);
- col-md 用于中屏设备 (min-width：992px);
- col-sm 用于小屏设备 (min-width：768px);
- col-xs 用于超小型设备 (max-width：768px)。

如果要兼容所有分辨率，则这四个 class 都要设置。

```html
<!- 一行分为三等份 ->
<div class="row">
    <div class="col-xs-4 col-sm-4 col-md-4 col-lg-4">
           lg=4 md=4 sm=4 xs=4
    </div>
        <div class="col-xs-4 col-sm-4 col-md-4 col-lg-4">
           lg=4 md=4 sm=4 xs=4
    </div>
    <div class="col-xs-4 col-sm-4 col-md-4 col-lg-4">
           lg=4 md=4 sm=4 xs=4
    </div>
</div>
```

?> 使用 `.col-*-offset-*` 类可以将列向右侧偏移 * 列。

## modal

- 关闭窗口：`$("#myModal").modal('hide');`

- modal 关闭时清空数据：

  ```javascript
  //为modal关闭后绑定方法
  $('#myModal').on('hidden.bs.modal', function () {
      $('form').trigger('reset');//调用Dom本身的表单reset功能
  })
  ```




## 问题

### 禁用状态和只读状态

输入框设置 `disabled` 和 `readOnly`  都能起到不能输入的效果，但是 disabled 状态的输入框不能被读取到，如：`$("#id,#name").serialize()` 。