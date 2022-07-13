# SpringMVC学习2



## 控制器Controller配置总结
### 控制器Controller
* 控制器负责提供访问应用程序的行为，通常用过**接口定义**或者**注解定义**两种方式
* 控制器负责将用户的一个请求转换成一个模型
* 在Spring MVC中一个控制器类包含多个方法
* SpringMVC中，对于控制器的实现方式有多种

### 控制器的实现方式
#### 实现接口Controller定义控制器
* 这种方式较为不常用，很老
* 缺点是一个控制器只有一个方法，如果要多个方法则需要定义多个Controller，定义的方式比较麻烦

#### 通过注解@Controller定义控制器
* @Controller代表这个类会被Spring接管，被这个注解的类中所有的方法，如果返回值是String，并且页面可以跳转，就会被视图解析器解析
* 可以一个控制器多个方法 
* 可以多个控制器跳转到同一个视图，但是页面的结果是不一样的，这里的视图是被复用的，控制器和视图之间是**弱耦合**的关系

## @RequestMapping
* 可以只加在方法上，用户路径就直接是 `/h1`
```Java
@RequestMapping("/h1")
public String hello(Model model){

    // 封装数据，模型中添加属性值msg，可以在jsp页面中取出并渲染
    model.addAttribute("msg", "Hello, SpringMVCAnnotation");

    // 拼接取得视图地址
    return "hello"; // return的结果会被视图解析器处理
}
```
* 可以同时加在类和方法上，路径就是类上的路径作为父路径叠加 `/hello/h1`
```Java
package com.swagger.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/hello")
public class HelloController {

    // 代表真实用户访问地址
    // localhost:8080/hello/h1
    @RequestMapping("/h1")
    public String hello(Model model){

        // 封装数据，模型中添加属性值msg，可以在jsp页面中取出并渲染
        model.addAttribute("msg", "Hello, SpringMVCAnnotation");

        // 拼接取得视图地址
        return "hello"; // return的结果会被视图解析器处理
    }

}
```
* 最好直接加在方法上，不要放在类上 

## Restful风格
* 传统变量添加方式 http://localhost:8080/add?a=1&b=2
* Restful变量添加方式 http://localhost:8080/add/a/b

**@PathVariable 路径变量**  
* 在变量前加上这个注解
* 且RestMapping变成 @RequestMapping("/add/{a}/{b}")

### restful接受参数传递方式
@RestMapping后加上name，和method方式  
```Java
@RequestMapping(name="/add/{a}/{b}", method=RequestMethod.GET)
```
其中传递类型包括：  
* RequestMethod.GET
* RequestMethod.POST
* RequestMethod.DELETE
* RequestMethod.PUT
* RequestMethod.PATCH










