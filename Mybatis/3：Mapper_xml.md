### 1：Mapper XML：映射文件配置

- cache：给定命名空间的缓存配置
- cache-ref：其他命名空间缓存配置的引用
- sql：可被其他语句重用sql语句
- resultMap：用来描述如何从数据库结果集中来加载对象
- select： 映射查询语句
- insert：  映射插入语句
- update：映射更新语句
- delete： 映射删除语句

### 2：select 属性

- ##### 简易数据类型

  - parameterType：定义参数类型，将会传入这条语句的参数类的完全限定名或别名
  - resultType（resultMap）：定义返回值类型，不可同时使用，实际上内部是创建一个ResultMap的

```xml
<!--类类型的参数对象，id、username 和 password 属性将会被查找，将它们的值传入预处理语句的参数中-->
<insert id="insertUser" parameterType="User">
  insert into users (id, username, password)
  values (#{id}, #{username}, #{password})
</insert>
<!--自动把列名作为key，value作为值存储-->
<select id="selectPerson" parameterType="int" resultType="map">
  SELECT * FROM PERSON WHERE ID = #{id}
</select>
<!--数据库列名和类属性不一致-->
<select id="selectUsers" resultType="person">
  select
    user_id             as "id",
    user_name           as "userName",
    hashed_password     as "hashedPassword"
  from some_table
  where id = #{id}
</select>
```

- ##### 自动映射功能（两种方式）

  - 只要返回 **数据库 列名和 JavaBean 的属性一致**，MyBatis 就会自动回填这些字段而无需任何配置
  - 如果你的数据库是规范命名的，即数据库每一个单词都用下划线分隔，POJO 采用驼峰式命名方法，可以设置 mapUnderscoreToCamelCase 为 true，也可以实现从数据库到 POJO 的自动映射
  - 自动映射可以在config.xml 中 settings 元素中配置 autoMappingBehavior 属性值来设置其策略
    - NONE：取消自动映射
    - PARTIAL：，辅助的，只会自动映射没有定义嵌套结果集映射的结果集
    - FULL：会自动映射任意复杂的结果集（无论是否嵌套）

```xml
<setting name="autoMappingBehavior" value="PARTIAL"/>
```

- ##### 用 Map 传递多个参数 或  返回Map

  1. map 传递多个参数时，parameterType="map"，传参时，Map的key 时参数，value是参数值，通过#{key}拿值
  2. map 作为返回值时，resultMap="map"
     - 返回Map 是一个数据时，根据数据库的字段和值生成map															
     - 返回 map 是个对象时，key是属性，value是值

- ##### useCache="ture"  此select语句使用二级缓存



### 3：insert，update，delete 的属性

- id：命名空间中的唯一标识符，选择使用接口的哪个方法
- useGeneratedKeys：逐渐回填，MyBatis 使用 JDBC的 getGeneratedKeys（）来获取数据库内部自动生成的
  - 默认值：false，仅对 insert 和 update 有用
- parameterType：传入语句的参数的完全限定类名或别名
- keyProperty：标记哪个属性是主键，默认：未设置（unset）
- flushCache：任何时候只要该语句被调用，都会导致本地缓存和二级缓存都会被清空，默认值：true
- timeout：抛出异常之前，驱动程序等待数据库返回请求结果的秒数,默认值： 未设置（unset）
- statementType :发送sql语句的方式，STATEMENT，PREPARED 或 CALLABLE,默认值：PREPARED	
- keyColumn：通过生成的键值设置表中的列名，这个设置在某些数据库（像 PostgreSQL）是必须的，当主键列不是表中的第一列的时候需要设置

```xml
<insert id="insertAuthor" useGeneratedKeys="true" keyProperty="id">
  insert into User (username,password,email,bio)
  values (#{username},#{password},#{email},#{bio})
</insert>
```

- ##### 主键回填


​	 MySQL 中主键自增字段,在插入后我们入往往需要获得这个主键，把获得的主键值给 JavaBean 的 ID

```xml
<insert id="insertUser" parameterType="user" useGeneratedKeys="true" keyProperty="id">
	insert into user(username,note) values (#{username},#{note})
</insert>
```

- ##### 字符串替换：参数占位

  - 使用${}会自动加上 ' ' ，用于字符串，会出现Sql注入问题
  - 使用#{}不会，替换值

- ##### sql包含：可以通过property替换值

```xml
<!-- sql元素包含 -->
<sql id="role_columns">
	id,name,note
</sql>
<select>
  <include refid="role_columns"> 
				<property name="name" value="lisi"/>
	</include> from t_role where id=#{id}
</select> 
```

##### 	

### 4：结果映射（ResultMap）

​	resultMap 定义的主要是一个结果集的映射关系，MyBatis 支持 resultMap 查询	

```xml
 <resultMap type="stu" id="stuscore">
     <id property="id" column="u_id"/>
     <result property="clazzId" column="clazz_id"/>
     <result property="name" column="name"/>
     <result property="sex" column="sex"/>
     <result property="age" column="age"/>
     
     <result property="score.math" column="math"/>
     <result property="score.english" column="english"/>
  </resultMap>
```



##### 一对一映射(one to one)

###### 1：内嵌对象

​	stu 中有一个属性为Score的类对象，使用圆点记法为内嵌的对象的属性赋值

```xml
<result property="score.math" column="math"/>
```

###### 2：ResultMap 继承 resultMap

```xml
<!-- 继承嵌套 -->
<resultMap type="stu" id="stus">
  <id property="id" column="id"/>
  <result property="clazzId" column="clazz_id"/>
  <result property="name" column="name"/>
  <result property="sex" column="sex"/>
  <result property="age" column="age"/>
</resultMap>  

<resultMap type="stu" id="stuscore" extends="stus">
  <result property="score.math" column="math"/>
  <result property="score.english" column="english"/>
  <result property="score.pe" column="pe"/>
</resultMap>
```

###### 3：< association> 引入 resultMap

​	被用来导入“有一个”(has-one)类型的关联

```xml
<association property="score" resultMap="ScoreResultMap"/>
```

###### 4：< association> 内联的 resultMap

```xml
<resultMap type="stu" id="stusMap">
     <id property="id" column="id"/>
     <result property="clazzId" column="clazz_id"/>
     <result property="name" column="name"/>
     <result property="sex" column="sex"/>
     <result property="age" column="age"/>
     
     <association property="score" javaType="Score" column="stu_id">
	      <id property="id" column="id"/>
	      <result property="math" column="math"/>
	      <result property="english" column="english"/>
     </association>
</resultMap> 
```

​	property="Type值的属性"，column="表中的列"，对应stu的score

###### 5：resultMap 嵌套 select查询

​	select 属性值设置为 <select> 属性id值，column 属性值将作为参数传递给 select 标签执行查询，返回结果

- column="表中的列作为参数"

```xml
<association property="score" select="findScorebyid" column="id"> </association>
```



#####    一对多映射(one to many)

###### 1：嵌套 select标签

​     < collection> 或 < association> 元素将一对多,类型的结果映射到 一个对象集合上

```xml
<!-- 一个班级对象多个学生 -->
<association property="stus" javaType="ArrayList" select="findStuByClassId" column="id" fetchType="lazy"> 
</association>  
<collection property="stus" javaType="ArrayList" select="findStuByClassId" column="id" fetchType="lazy">
</collection>
```

**fetchType.LAZY** ：懒加载，加载一个实体时，定义懒加载的属性不会马上从数据库中加载
**fetchType.EAGER**：急加载，加载一个实体时，定义（饿）急加载的属性会立即从数据库中加载



#####   多对多映射(many to many)

​	1：嵌套select 语句，一对多主要是三个表之间的关系

```xml
<select id="findStuByTeacherId" resultMap="stusMap">
  select s，st from stu_tea st,stu s where st.tea_id=#{id} and st.stu_id=s.id
</select>
```



```xml
<resultMap type="Teacher" id="teachMap">
   	<id property="id" column="id"/>
     <result property="sex" column="sex"/>
     <result property="age" column="age"/>
     <result property="course" column="course"/>
     
  <collection property="stus" fetchType="lazy" select="findStuByTeacherId" column="id">
  </collection>
</resultMap>
```



### 5：缓存：存储内容访问命中率

- 一级缓存：默认情况下，开启一级缓存，一级缓存只是相对于同一个 SqlSession
- 二级缓存：默认情况下，不开启二级缓存，二级缓存是 SqlSessionFactory 层面上的缓存，关闭会话连接仍然缓存值
  - MyBatis要求返回的POJO必须是可序列化的，也就是要求实现Serializable接口
  - 开启配置：在mapper.xml 文件中 配置 <cache>  就可以开启二级缓存,useCache="true"
  - <cache /> 全局配置开启，很多设置是默认的，如下
  - <cache eviction="LRU" flushInterval="100000" size="1024" readOnly="true"/>
    - eviction：代表是缓存置换算法，默认：LRU(Least Recently Used)
      - LRU：最近最少使用，移除最长时间不用的对像
      - FIFO：先进先出，按对像进入缓存的顺序来移除它们
    - flushInterval：缓存刷新间隔时间，单位为毫秒，如果你不配置它，那么当 SQL 被执行时，会自动刷新缓存
    - size：引用数目，一个正整数，代表缓存最多可以存储多个对象，不宜设置过大，设置过大会导致内存溢出
    - readOnly：只读，意味着缓存数据只能读取而不能修改，它的默认值为 false


