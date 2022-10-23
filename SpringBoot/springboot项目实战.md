# 一、架构：创建包

1.entity：实体包

2.mapper：

3.service：服务层

4.controller：控制器层

5.utils：通用的一些类

6.framework：整合第三方的东西



## 1.entity：对应数据库表

| mysql  | java    |
| ------ | ------- |
| int    | Integer |
| vachar | String  |
| date   | Date    |
| char   | String  |

一般一个字段，对应

一个private xxx，

一个getxxx()方法，

一个setxxx()方法。



## 2.mapper：包括xxxMapper.java或者是xxxDao.java，和一个xxxMapper.xml（1或者1，+1）

1.首先是xml文件的头

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.项目名.mapper.xxxMapper">
    <!--待补充-->
</mapper>
```

2.然后是再xxxMapper.java或者是xxxDao.java里面写方法

```java
public int create(User user);//增，传对象
public int delete(Map<String,object> paramMap);//删，传map参数
public int update(Map<String,object> paramMap);//改，传map参数
public List<User> query(Map<String,object> paramMap);//查1，按条件查询，传map参数
public User detail(Map<String,object> paramMap);//查2，查询详情，返回一个对象，传map参数
public int count(Map<String,object> paramMap);//查3，查记录数，传map参数
```

3.对于给定方法，对xml文件进行补充

```xml
<mapper namespace="com.项目名.mapper.xxxMapper">
    
    <resultMap id="BaseResultMap" type="com.项目名.entity.xxx">
        <id column="id" property="id" />
        <result column="字段1名" property="类对应字段名" />
        <result column="字段2名" property="类对应字段名" />
        <result column="字段N名" property="类对应字段名" />
    </resultMap>

<!--增-->
    <!--id对应类中的方法名，parameterType对应传入参数-->
    <insert id="create" parameterType="com.项目名.entity.xxx">
        <!--sql语句-->
        insert into 表名(字段1,字段2,等等,字段N)
        values(#{对应类字段1},#{对应类字段2},#{对应类字段等等},#{对应类字段N})
    </insert>

<!--删-->
    <!--删除，id对应类中的方法名，因为是传的map，会扩展使用-->
    <delete id="delete">
        delete from 表名
        <include refid="findCriteria"></include>
    </delete>
    
    <!--查询标准，扩展的使用，对应上面的findCriteria-->
    <sql id="findCriteria">
        <where>
            <if test="id != null">
                and id = #{id}
            </if>
            <if test="字段1 != null and 字段1 != ''">
                and 字段1 = #{类对应字段1的数据}
            </if>
            <if test="字段2 != null and 字段2 != ''">
                and 字段2 = #{类对应字段2的数据}
            </if>
            <if test="字段N != null and 字段N != ''">
                and 字段N = #{类对应字段N的数据}
            </if>
        </where>
    </sql>
    
<!--改-->
    <!--修改，id对应类中的方法名，因为是传的map，会扩展使用-->
    <update id="update">
        update 表名
        <include refid="updateCriteria"></include>
        <include refid="findCriteria"></include>
    </update>
    
    <!--map传入的对象是带update前缀的-->
    <!--更新标准，扩展的使用，对应上面的updateCriteria-->
    <sql id="updateCriteria">
        <set>
            <if test="updateId != null">
                id = #{{updateId},
            </if>
            <if test="update字段1 != null and update字段1 != ''">
                字段1 = #{update类对应字段1的数据},
            </if>
            <if test="update字段2 != null and update字段2 != ''">
                字段2 = #{update类对应字段2的数据},
            </if>
            <if test="update字段N != null and update字段N != ''">
                字段N = #{update类对应字段N的数据},
            </if>
        </set>
    </sql>
    
    
    
<!--查-->
    <!--id对应类中的方法名，resultMap是返回的map-->
    <select id="query" resultMap="BaseResultMap">
        select * from 表名
        <include refid="findCriteria"></include>
    </select>
    
    <!--查询详情，返回一个对象-->
    <select id="detail" resultMap="BaseResultMap">
        select * from 表名
        <include refid="findCriteria"></include>
        <!--根据查询只留一条数据【limit i,n】i是索引下标从0开始，可省略，n是数量-->
        limit 1
    </select>
    
    <!--查记录数，resultType是返回的类型-->
    <select id="count" resultType="int">
        <!--COUNT() 函数返回匹配指定条件的行数，NULL不计入-->
        select count(1) from 表名
        <include refid="findCriteria"></include>
    </select>
    
    
    
</mapper>
```



## 3.service编写

1.添加@Service注解以及对应包。如果是实现与声明分离，记得将注释架在实现上

2.调用对应xxxMapper.java里写的类，并添加上@Autowired以及包

```java
@Autowired
private UserMapper userMapper;
```

3.以及添加对应的方法的实现

```java
/*增，传对象*/
public int create(User user){}
/*删，传map参数*/
public int delete(Map<String,object> paramMap){}
/*改，传map参数*/
public int update(Map<String,object> paramMap){}
/*查1，按条件查询，传map参数*/
public List<User> query(Map<String,object> paramMap){}
/*查2，查询详情，返回一个对象，传map参数*/
public User detail(Map<String,object> paramMap){}
/*查3，查记录数，传map参数*/
public int count(Map<String,object> paramMap){}
```



## 4.controller编写

1.添加@Controller注解以及对应包

2.添加@RequestMapping("/xxx")，xxx对应的是请求的url

3.调用对应service里的类，添加上@Autowired以及包

4.编写方法，@RestController：就是@Controller加上@ResponseBody

```java
@Controller
@RequestMapping("/user")
public class UserController
{
    @Autowired
	private UserService userService;
    
    @PostMapping("/create")
    /*或者这么写
     @RequestMapping(value="/create",method = RequestMethod.POST)
     @RequestMapping(value={”create“,"/create"},method = RequestMethod.POST)
    */
    /*@RequestBody表示传入的是json数据，如果需要返回json数据到前端，也需要再方法前加上该注解*/
    public void create(@RequestBdoy User user){
        userService.create(user);
    }
}
```



# 二、细化项目

## 1.重点细化service

细化业务

原始基本代码

```java
public int delete(Integer id){
    Map<String,Object> map = new HashMap<>();
    map.put("id",id);
    return userDao.delete(map);
}
```



## 2.对map进行封装

在utils里添加通用工具类Maps，让map可以链式调用。基本都封装在add中

```java
/**
  *每次调用的时候都会传来key和value
  */
public class Maps
{
    private Map<String,Object> paramMap = new HashMap<>();
    
    /*私有构造方法*/
    private Maps(){
        
    }
    
    private Maps(String key,String value){
        paramMap.put(key,value);
    }
    private Maps(String key,Object value){
        paramMap.put(key,value);
    }
    private Maps(Integer id){
        paramMap.put("id",id);
    }
    
    /*1.build方法目的是构建Maps对象，因为Maps对象有一个Map集合（支持构建传参数）*/
    public static Maps build(String key,String value){
        return new Maps(key,value);
    }
    public static Maps build(String key,Object value){
        return new Maps(key,value);
    }
    
    /*2.build方法目的是构建Maps对象，因为Maps对象有一个Map集合（构建简单Map对象）*/
    public static Maps build(){
        return new Maps();
    }
    
    /*3.直接传id构建map*/
    public static Maps build(Integer id){
        return new Maps(id);
    }
    
    /*对Map进行操作，向map中追加key-value*/
    public Maps put(String key,Object value){
        return add(key,value);
    }
    
    public Maps add(String key,Object value){
        paramMap.put(key,value);
        return this;
    }
    
    /*向map集合中追加id*/
    public Maps addId(Integer id){
        paramMap.put("id",value);
        return this;
    }
    
    public Maps putId(Integer id){
        return add(id);
    }
    
    /*对Map进行操作，向map对象中追加map对象*/
    public Maps add(Map<String,Object> map){
        for(Map.Entry<String,Object> entry:map.entrySet()){
            paramMap.put(entry.getKey(),entry.getValue());
        }
        return this;
    }
    
    public Maps put(Map<String,Object> map){
        return add(map);
    }
    
    /*对Map进行操作，获取最终的Map集合*/
    public Map<String,Object> getMap(){
        return paramMap;
    }
    
}
```

## 3.使用封装后的map进行操作

service层

```java
public int delete(Integer id){
    //return userDao.delete(Maps.build(id).getMap());
    return userDao.delete(Maps.build("id",id).getMap());
}
```

controller层

```java
@PostMapping("/delete")
public void delete(Integer id){
    userService.delete(id);
}
```

## 4.修改和更新

传入的是实体类对象到Controller

但是在service层是使用map操作的，需要在service层，将传入的实体类对象转为后续需要的map

```java
public int update(User user){
    Map<String,Object> map = new HashMap<>();
    map.put("id",user.getId());
    map.put("userName",user.getUserName());
    return userDao.update(map);
}
```

工具类Maps添加

```java
/*对象转map集合*/
public <T> Map<String,Object> beanToMap(T bean){
    if(bean!=null){
        BeanMap beanMap=BeanMap.create(bean);
        for(Object key : beanMap.keySet()){
            paramMap.put("update"+upperFirstLetter(key+""),beanMap.get(key));
        }
    }
    return paramMap;
}
/*首字母大写转换*/
public static String upperFirstLetter(String string){
    char[] chars=str.toCharArray();
    if(chars[0]>='a' && chars[0]<='z'){
        chars[0]=(char)(chars[0]-32);
    }
    return new String(chars);
}
```

添加工具类后的新service层

```java
public int create(User user){
    return userDao.create(user);
}

public int delete(Integer id){
    return userDao.update(Maps.build(id).getMap());
}

public int update(User user){
    return userDao.update(Maps.build(user.getId()).beanToMap(user));
}

public List<User> query(User user){
    return userDao.query(Maps.build().beanToMap(user));
}

public List<User> detail(Integer id){
    return userDao.detail(Maps.build(id).getMap());
}

public List<User> count(User user){
    return userDao.count(Maps.build().beanToMap(user));
}
```

## 5.Controller补充编写

添加工具类，对Controller返回数据的补充

```java
public class Result
{
    public statis Integer SUCCESS_CODE=200;
    public statis Integer ERROR_CODE=500;
    public Integer status=SUCCESS_CODE;//状态，成功-200，服务器内部错误-500，404等等
    public String msg="True";//返回的消息，成功或者失败
    public Object data=null;//响应的数据
    
    /*成功响应状态*/
    public static Result ok(Integer status,String msg,Object data){
        return new Result(status,msg,data);
    }
    
    public static Result ok(String msg,Object data){
        return new Result(msg,data);
    }
    
    public static Result ok(Object data){
        return new Result(data);
    }
    public static Result ok(){
        return new Result();
    }
    
    /*失败响应*/
    public static Result fail(Integer status,String msg,Object data){
        return new Result(status,msg,null);
    }
    public static Result fail(Sting msg){
        return new Result(ERROR_CODE,msg,null);
    }
    public static Result fail(){
        return new Result(ERROR_CODE,"False");
    }
    
    /*构造函数*/
    public Result(Integer status,String msg,Object data){
        this.status=status;
        this.msg=msg;
        this.data=data;
    }
    public Result(String msg,Object data){
        this.msg=msg;
        this.data=data;
    }
    public Result(Object data){
        this.data=data;
    }
        public Result(Integer status,String msg){
        this.status=status;
        this.msg=msg;
    }
    public Result(){
        
    }
    
	/*对应三个get和set方法省略*/
    ......
    
}
```

修改Controller

```java
@PostMapping("/create")
public void create(@RequestBdoy User user){
        userService.create(user);
    }

@PostMapping("/delete")
public void delete(Integer id){
    userService.delete(id);
}

@PostMapping("/update")
public void update(@RequestBdoy User user){
    userService.update(user);
}

@PostMapping("/query")
public Result query(@RequestBdoy User user){
    List<User> list = userService.query(user);
    return Result.ok(list);
}

@PostMapping("/detail")
public Result delete(Integer id){
    User user = userService.delete(id);
    return Result.ok(user);
}

@PostMapping("/count")
public Result count(@RequestBody User user){
    Integer count = userService.count(user);
    return Result.ok(count);
}

```

添加状态类

```java
public class Result
{
    public static Integer SUCCESS_CODE = 200;
    public static Integer ERROR_CODE = 500;
    public Integer status = SUCCESS_CODE;
    public String msg = "操作成功";
    public Object data = null;
    
    //失败响应
    public static Result fail(Integer status, String msg){
        return new Result(status, msg, null);
    }
 
    
    public static Result fail(String msg){
        return new Result(ERROR_CODE, msg, null);
    }
    
    public static Result fail(){
        return new Result(ERROR_CODE, "操作失败");
    }
    
    //成功响应
    public static Result ok(Integer status, String msg, Object data){
        return new Result(status, msg, data);
    }
    
    public static Result ok(String msg, Object data){
        return new Result(msg data);
    }
    
    public static Result ok(Object data){
        return new Result(data);
    }
    
    public static Result ok(){
        return new Result();
    }
    
    //构造重载
    public Result(){
        
    }
    
    public Result(Object data){
        this.data = data;
    }
    
    public Result(String msg,Object data){
    	this.msg = msg;  
        this.data = data;
    }
    
    public Result(Integer status, String msg){
        this.status = status;
        this.msg = msg;
    }
    
    public Result(Integer status, String msg, Object data){
        this.status = status;
        this.msg = msg;  
        this.data = data;
    }
    
    
    //...get与set
    
}
```



## 6.全局异常处理

在framework文件夹下建立一个mvc文件夹，再来个类文件，类名叫MyControllerAdvice.java

```java
@ControllerAdvice
public class MyControllerAdvice
{
    @ExceptionHandler(RuntimeException.class)
    @ResponseBody
    public Result handle(RuntimeException exception){
        return Result.fail(exception.getMessage());
    }
}
```

待补充

## 7.分页查询

Pagehelper分页，

在pom.xml中加入依赖

```xml
<!--pagehelper 分页插件-->
<dependency>
	<groupId>com.github.pagehelper</groupId>
	<artifactId>pagehelper-spring-boot-starter</artifactId>
	<version>1.2.3</version>
</dependency>
```

分页关键字limit，【limit i,n】i是索引下标从0开始，可省略，n是显示的数量，也就是数据库条数

在web页面中做分页的时候，用到pageNo，pageSize

```sql
select * from 【表名】 limit (1-1)*10,10 #第一页
select * from 【表名】 limit (2-1)*10,10 #第二页
select * from 【表名】 limit (3-1)*10,10 #第三页
select * from 【表名】 limit (4-1)*10,10 #第四页
```

分页插件使用举例

```java
//获取第1页，10条内容，默认查询总数count
PageHelper.startPage(1, 10);
//紧跟着的第一个select方法会被分页
List<User> list = userMapper.selectIf(1);
assertEquals(2, list.get(0).getId());
assertEquals(10, list.size());
//分页时，实际返回的结果list类型是Page<E>，如果想取出分页信息，需要强制转换为Page<E>
assertEquals(182, ((Page) list).getTotal());
====================================================================
//获取第1页，10条内容，默认查询总数count
PageHelper.startPage(1, 10);
List<User> list = userMapper.selectAll();
//用PageInfo对结果进行包装
PageInfo page = new PageInfo(list);
//测试PageInfo全部属性
//PageInfo包含了非常全面的分页属性
assertEquals(1, page.getPageNum());
assertEquals(10, page.getPageSize());
assertEquals(1, page.getStartRow());
assertEquals(10, page.getEndRow());
assertEquals(183, page.getTotal());
assertEquals(19, page.getPages());
assertEquals(1, page.getFirstPage());
assertEquals(8, page.getLastPage());
assertEquals(true, page.isFirstPage());
assertEquals(false, page.isLastPage());
assertEquals(false, page.isHasPreviousPage());
assertEquals(true, page.isHasNextPage());
```

定义实体类进行分页操作

```java
public class Entity
{
    private Integer page;//页号
    private Integer limit;//页面大小
    
    /*对应的get和set方法*/
    ......
}
```

然后让实体类继承这个类

```java
public class User extends Entity
{
	......
}
```

对于query的service层

```java
public PageInfo<User> query(User user){
    if(user != null && user.getPage() != null){
        PageHelper.startpPage(user.getPage(), user.getLimit());
    }
    List<User> list = userDao.query(Maps.build().beanToMap(user));
    return new PageInfo<User>(list);
}
```

对于query的controller层

```java
@PostMapping("/query")
public Result query(@RequestBody User user){
    PageInfo<User> pageInfo = userService.query(user);
    return Result.ok(pageInfo);
}
```



# 三、多表查询

