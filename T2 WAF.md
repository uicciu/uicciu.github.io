# T2 WAF

> 题目： [RoarCTF 2019]Easy Calc

__0x00 审题___

1. 打开网页，是一个计算器，随意输入算式得到结果（尝试输入字符，发现回显错误）
2. 查看源码，如下
```html
<!--I've set up WAF to ensure security.-->
<script>
    $('#calc').submit(function(){
        $.ajax({
            url:"calc.php?num="+encodeURIComponent($("#content").val()),
            type:'GET',
            success:function(data){
                $("#result").html(`<div class="alert alert-success">
            <strong>答案:</strong>${data}
            </div>`);
            },
            error:function(){
                alert("这啥?算不来!");
            }
        })
        return false;
    })
</script>
```
> 发现存在问题：
