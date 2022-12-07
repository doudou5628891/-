# 1.AJAX与SpringBoot后端

```js
$.ajax({
    url:"/getCommonsConditionalQueryResult",
    //context: ('#btn_loadchange'),
    type: 'post',
    async: true,
    datatype: 'json',
    data:{
        sps_condition: JSON.stringify(selected_item)
    },
    success: successCallBack,
    error: errorCallBack,
    complete: completeCallBack
});
```

JSON.stringify()是一种序列化方法





```java
@PostMapping({"/getCommonsConditionalQueryResult"})
@ResponseBody // 返回结果直接放入html中
public List<WF_SpsBean> getCommonsConditionalQueryResult(HttpServletRequest request,Model model){
	System.out.println(request.getParameter("sps_condition"));	
    return null;
}

//@RequestBody 是
public List<WF_SpsBean> getCommonsConditionalQueryResult(@RequestBody String sps_condition,Model model){
// 在后端的同一个接收方法里，@RequestBody与@RequestParam()可以同时使用，@RequestBody最多只能有一个，而@RequestParam()可以有多个。
    
   
    return null;
}


```





