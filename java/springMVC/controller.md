# Controller类

## @RequestParam
用来绑定请求参数到方法参数上。

```java
@Controller  
@RequestMapping("/pets")  
public class EditPetForm {  
    @RequestMapping(method = RequestMethod.GET)  
    public String setupForm(@RequestParam("petId") int petId, ModelMap model) {  
        //...
    }  
```

## @PathVariable
当使用@RequestMapping URI template 映射时（uri/{paramId}），此时 paramId 可以通过`@PathVariable`注解绑定它传来的值到方法的参数上。

```java
@Controller  
@RequestMapping("/owners/{ownerId}")  
public class RelativePathUriTemplateController {  
    @RequestMapping("/pets/{petId}")  
    public void findPet(@PathVariable String ownerId, @PathVariable String petId, Model model) {      
        //...  
    }  
}  
```

## @RequestHeader @CookieValue
`@RequestHeader`可以把 Request 请求 header 部分的值绑定到方法的参数上，同理@CookieValue 可以把 header 中 cookie 的值绑定到方法的参数上。

以下是某 request 的 header 部分：

| key             | value          |
| --------------- | -------------- |
| Host            | localhost:8080 |
| Accept-Encoding | gzip,deflate   |
| Keep-Alive      | 300            |

```java
@RequestMapping("/displayHeaderInfo")  
public void displayHeaderInfo(@RequestHeader("Accept-Encoding") String encoding, @RequestHeader("Keep-Alive") long keepAlive)  {  
    //...  encoding的值就被设置为gzip,deflate
}  
```

### @RequestBody @ResponseBody
这俩个注解需要 jackson-jar 包的支持

1. @RequestBody：把前台页面传递的 json 格式强制转换为 javaBean
2. @ResponseBody：把后台 javaBean 转换为 json 数据格式返回给前台页面

```java
@RequestMapping("jsonTest")
public @ResponseBody Student jsonTest(@RequestBody Student student) {
    return student;
}
```