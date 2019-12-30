# Thymeleaf

[官方文档](https://www.thymeleaf.org/documentation.html)
[详解](https://o7planning.org/en/12345/thymeleaf)


- [Thymeleaf](#thymeleaf)
  - [Thymeleaf Fragment](#thymeleaf-fragment)
    - [Fragment](#fragment)
    - [th:insert, th:replace, th:include](#thinsert-threplace-thinclude)
    - [Fragment with parameters](#fragment-with-parameters)
    - [th:assert](#thassert)


## Thymeleaf Fragment

### Fragment

The important thing when you want to import a  Fragment of a  Template is that you have to describe its position.
Th syntax for describing the position of a Fragment of a  Template:
> ~{/path-to-template/template-name :: selector}  

If you want to describe the position of Fragment of current  Template, it is possible to use a shorter syntax:

> ~{:: selector}  
> ~{this :: selector}  

usage:
1. Select a  fragment by name
   1. `~{templatename::fragmentname}`, fragment来自`(th:fragment)`的声明
2. Choose a  fragment by tag name
   1. `~{templatename::tagname}`, 如`~{my-template::head/link}`会将my-template页面``<head>``标签内所有的link标签复制过来
3. Select the  fragment by the value of the ID attribute of tag
   1. `~{templatename::#id}`,通过元素id单独引入一个元素
4. Select  fragment by  Css Class
   1. `~{templatename:: .classname},~{templatename:: tagname.classname}   (Css Class)`
5. Select all in  Template
   1. `~{templatename}  (Everything)`   
6. Others...
   1. `~{fragments/my-template :: #tagId/text() }` 
   2. `~{fragments/my-template :: fragmentName/text() }`
   3. `~{fragments/my-template :: tagName/text() }`

### th:insert, th:replace, th:include

1. th:insert
   1. th:insert will insert Fragment to act as the child of Target tag.
   2. 将模板碎片作为子元素完全插入当前元素内部
2. th:replace
   1. th:replace will replace target tag with Fragment.
   2. 用模板碎片完全替换当前元素
3. th:include(thymeleaf3不推荐使用)
   1. th:include will insert the child of Fragment to act as the child of Target tag.
   2. 用模板碎片内部的所有元素替换当前元素内部

```html
    <body>
  ...

        <div th:insert="footer :: copy"></div>

        <div th:replace="footer :: copy"></div>

        <div th:include="footer :: copy"></div>
    </body>
```
result:
```html
    <body>

  ...

        <div>
            <footer>
      &copy; 2011 The Good Thymes Virtual Grocery
            </footer>
        </div>

        <footer>
    &copy; 2011 The Good Thymes Virtual Grocery
        </footer>

        <div>
    &copy; 2011 The Good Thymes Virtual Grocery
        </div>
    </body>
```

### Fragment with parameters

A fragment will be like a function if it add parameters and luckily, the  Thymeleaf supports this.

1. Explicit parameters:明确的参数
```html
    <div th:replace="~{fragments/my-template2 :: person( firstName='Donald', lastName= 'Trump') }"></div>

    <div th:replace="~{fragments/my-template2 :: person( ${u.firstName}, ${u.lastName}) }"></div>
```
2. Implicit parameter:隐含的参数
```html
        <!-- Fragment with implicit parameters. -->
    <div th:fragment="greeting" class="box">
        <p>Hello
            <span th:utext="${title}"></span>
            <span th:utext="${name} ?: 'There'"></span>
        </p>
    </div>
```
Call  fragment which has implicit parameters
```html
    <div th:replace="~{fragments/my-template2 :: greeting(title='Mr.', name = 'Tom') }"></div>

    <div th:replace="~{fragments/my-template2 :: greeting }"></div> 
```
3. 使用~{}传递空参数
4. 使用_使用模板中的参数作为默认值

### th:assert

th:assert帮助我们执行一个或多个表达式(多个表达式用逗号分割),如果所有的表达式的执行结果都为true,将没有问题,任何一个表达式执行结果为false都会抛出异常

```html
    <div th:fragment="employee (firstName, lastName)" class="box" th:assert= "${!#strings.isEmpty(firstName)}, ${!#strings.isEmpty(lastName)}">

        <p>First Name: <span th:utext="${firstName}"></span>
        </p>
        <p>Last Name: <span th:utext="${lastName}"></span>
        </p>
        <p>Full Name: <span th:utext="${firstName} + ' '+ ${lastName}"></span>
        </p>

    </div>
```