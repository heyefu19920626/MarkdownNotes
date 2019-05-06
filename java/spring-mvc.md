#### 跨域设置

- [参考](https://www.cnblogs.com/asfeixue/p/4363372.html)
- spring4.0+
```
<mvc:cors>
        <mvc:mapping path="/**" allowed-origins="*" allow-credentials="true" max-age="1800" allowed-methods="GET,POST,OPTIONS"/>
</mvc:cors>
```