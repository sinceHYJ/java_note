具体参考博客地址：<https://blog.csdn.net/biandous/article/details/65630783>

Example是用于查询条件的方便拼接。



简单的使用方法如下：

```java
cityExample.createCriteria()
                .andCityIdEqualTo(id)
        .andCityBetween("Ad","B");
List<City> ctys =  cityMapper.selectByExample(cityExample);
```

