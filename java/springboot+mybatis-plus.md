# 第一版

## 目录结构

![image-20240104171448959](C:\Users\17209\AppData\Roaming\Typora\typora-user-images\image-20240104171448959.png)

**这里有个问题就是：建实体类的时候表名设置的是eb_atest,但是找的却是atest表**

## 1、建表

![image-20240104171335896](C:\Users\17209\AppData\Roaming\Typora\typora-user-images\image-20240104171335896.png)

## 2、创建aTest实体类

![image-20240104171536489](C:\Users\17209\AppData\Roaming\Typora\typora-user-images\image-20240104171536489.png)

```
package com.zbkj.test.entity;

import com.baomidou.mybatisplus.annotation.TableName;
import lombok.Data;

/**
 * 功能：
 * 作者：LaoT
 * 日期：2024/1/4 14:56
 */
@Data
@TableName("eb_atest")
public class ATest {
    private Long id;
    private String name;
    private Integer age;
}
```

crmeb项目的实体类应该在crmeb-common的model中

![image-20240104172108769](C:\Users\17209\AppData\Roaming\Typora\typora-user-images\image-20240104172108769.png)

## 3、在 Mybatis-plus 的配置文件中添加 aTest 的映射

Mybatis配置，在这里定义了xml扫描位置

![image-20240104172307873](C:\Users\17209\AppData\Roaming\Typora\typora-user-images\image-20240104172307873.png)

![image-20240104172204112](C:\Users\17209\AppData\Roaming\Typora\typora-user-images\image-20240104172204112.png)



```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.zbkj.test.mapper.ATestMapper">

    <resultMap id="BaseResultMap" type="com.zbkj.test.entity.ATest">
        <id column="id" property="id"/>
        <result column="name" property="name"/>
        <result column="age" property="age"/>
    </resultMap>

    <select id="selectById" resultMap="BaseResultMap">
        select * from aTest where id=#{id}
    </select>

    <insert id="insert" parameterType="com.zbkj.test.entity.ATest">
        insert into aTest(name, age) values (#{name}, #{age})
    </insert>

    <update id="updateById" parameterType="com.zbkj.test.entity.ATest">
        update aTest set name=#{name}, age=#{age} where id=#{id}
    </update>

    <delete id="deleteById" parameterType="java.lang.Long">
        delete from aTest where id=#{id}
    </delete>

</mapper>
```

## 4、接着，创建 ATestMapper 接口：

![image-20240104172921347](C:\Users\17209\AppData\Roaming\Typora\typora-user-images\image-20240104172921347.png)

```
package com.zbkj.test.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.zbkj.test.entity.ATest;
import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface ATestMapper extends BaseMapper<ATest> {
}
```



## 5、创建service

![image-20240104173027028](C:\Users\17209\AppData\Roaming\Typora\typora-user-images\image-20240104173027028.png)

```
package com.zbkj.test.service;

import com.zbkj.test.mapper.ATestMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * 功能：
 * 作者：LaoT
 * 日期：2024/1/4 14:02
 */
@Service
public class aTestService {
    private final ATestMapper aTestMapper;

    @Autowired
    public aTestService(ATestMapper aTestMapper) {
        this.aTestMapper = aTestMapper;
    }
//
//    public List<aTest> getAll(){
//        return aTestMapper.selectList(null);
//    }
//
//    public aTest getAtestById(Long id){
//        return aTestMapper.selectById(id);
//    }
//
//    public void addAtest(aTest aTest){
//        aTestMapper.insert(aTest);
//    }
//
//    public void updateAtest(aTest aTest){
//        aTestMapper.updateById(aTest);
//    }
//
//    public void deleteAtest(Long id){
//        aTestMapper.deleteById(id);
//    }
}
```

## 6、最后，创建 ATestController 控制器：

**注意：正常情况，controller可以位于当前目录下，但是由于我要将该接口使用于移动端，因此我将其放在crmeb-front中**

![image-20240104173310945](C:\Users\17209\AppData\Roaming\Typora\typora-user-images\image-20240104173310945.png)

```
package com.zbkj.front.controller;

import com.zbkj.common.response.CommonResult;
import com.zbkj.test.entity.ATest;
import com.zbkj.test.mapper.ATestMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

/**
 * 功能：
 * 作者：LaoT
 * 日期：2024/1/4 15:43
 */
@RestController
@RequestMapping("api/front/aTest")
public class ATestController {
    @Autowired
    private ATestMapper aTestMapper;

    @GetMapping("/test")
    public CommonResult<ATest> test() {
        return CommonResult.success(aTestMapper.selectById(1));
    }

    @GetMapping("/{id}")
    public CommonResult<ATest> get(@PathVariable Long id) {
        ATest result = aTestMapper.selectById(id);
        System.out.println(result);
        if (result != null) {
            return CommonResult.success(result);
        } else {
            return CommonResult.failed("未找到对应的数据");
        }
    }

    @PostMapping
    public CommonResult<Boolean> add(@RequestBody ATest aTest) {
        boolean success = aTestMapper.insert(aTest) > 0;
        if (success) {
            return CommonResult.success(true);
        } else {
            return CommonResult.failed("添加失败");
        }
    }

    @PutMapping
    public CommonResult<Boolean> update(@RequestBody ATest aTest) {
        boolean success = aTestMapper.updateById(aTest) > 0;
        if (success) {
            return CommonResult.success(true);
        } else {
            return CommonResult.failed("更新失败");
        }
    }

    @DeleteMapping("/{id}")
    public CommonResult<Boolean> delete(@PathVariable Long id) {
        boolean success = aTestMapper.deleteById(id) > 0;
        if (success) {
            return CommonResult.success(true);
        } else {
            return CommonResult.failed("删除失败");
        }
    }
}
```

## 问题：

**1、mapper没扫描到，应该是mapper扫描位置没设置好**

在启动程序的位置添加mapper扫描，根据上一行mapper应该放于dao中

![image-20240104172517784](C:\Users\17209\AppData\Roaming\Typora\typora-user-images\image-20240104172517784.png)



# 第二版

## 1.创建数据库表格

## 2.定义实体类

```
package com.zbkj.test.entity;

import com.baomidou.mybatisplus.annotation.TableName;
import lombok.Data;

@Data
@TableName("atest")
public class ATest {
    private Long id;
    private String name;
    private Integer age;
}
```

## 3.创建mapper层

```
package com.zbkj.test.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.zbkj.test.entity.ATest;
import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface ATestMapper extends BaseMapper<ATest> {
}
```

## 4.创建service层

```
package com.zbkj.test.service;

import com.zbkj.test.entity.ATest;
import com.zbkj.test.mapper.ATestMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;


@Service
public class aTestService {
    private final ATestMapper aTestMapper;

    @Autowired
    public aTestService(ATestMapper aTestMapper) {
        this.aTestMapper = aTestMapper;
    }

    public ATest getAtestById(Long id){
        return aTestMapper.selectById(id);
    }

    public void addAtest(ATest aTest){
        aTestMapper.insert(aTest);
    }

    public void updateAtest(ATest aTest){
        aTestMapper.updateById(aTest);
    }

    public void deleteAtest(Long id){
        aTestMapper.deleteById(id);
    }
}
```

## 5.创建controller层

```
package com.zbkj.front.controller;

import com.zbkj.common.response.CommonResult;
import com.zbkj.test.entity.ATest;
import com.zbkj.test.mapper.ATestMapper;
import com.zbkj.test.service.aTestService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("api/front/aTest")
public class ATestController {
    @Autowired
    private ATestMapper aTestMapper;

    @Autowired
    private aTestService aTestService;

    @GetMapping("/test")
    public CommonResult<ATest> test() {
        return CommonResult.success(aTestService.getAtestById(1L));
    }

    @GetMapping("/{id}")
    public CommonResult<ATest> get(@PathVariable Long id) {
        ATest result = aTestService.getAtestById(id);
        System.out.println(result);
        if (result != null) {
            return CommonResult.success(result);
        } else {
            return CommonResult.failed("未找到对应的数据");
        }
    }

    @PostMapping
    public CommonResult<ATest> add(@RequestBody ATest aTest) {
        boolean success = aTestMapper.insert(aTest) > 0;
        if (success) {
            return CommonResult.success(aTest);
        } else {
            return CommonResult.failed("添加失败");
        }
    }

    @PutMapping
    public CommonResult<ATest> update(@RequestBody ATest aTest) {
        boolean success = aTestMapper.updateById(aTest) > 0;
        if (success) {
            return CommonResult.success(aTest);
        } else {
            return CommonResult.failed("更新失败");
        }
    }

    @DeleteMapping("/{id}")
    public CommonResult<Boolean> delete(@PathVariable Long id) {
        boolean success = aTestMapper.deleteById(id) > 0;
        if (success) {
            return CommonResult.success(true);
        } else {
            return CommonResult.failed("删除失败");
        }
    }
}
```
