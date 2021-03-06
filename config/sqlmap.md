# SmartSqlMap

| 属性       |    说明   |
| :--------- | --------:|
| Scope    | 域,用于SqlMap定义Sql声明范围  |

## Statement标签

| 属性       |    说明   |
| :--------- | --------:|
| Id    | 唯一性编号  |
| Cache    | 缓存策略编号,引用自Cache标签  |

## Statement 筛选子标签

| 标签           |    真条件   |
| :---------     | --------:|
| IsEmpty        | null or 空字符串 or 空IEnumerable |
| IsEqual        | 与比较值相等 |
| IsFalse        | 参数为 false  |
| IsGreaterEqual | 参数大于等于比较值     |
| IsGreaterThan  | 参数大于比较值        |
| IsLessEqual    | 参数小于等于比较值    |
| IsLessThan     | 参数小于比较值        |
| IsNotEmpty     | !(null or 空字符串 or 空IEnumerable)         |
| IsNotEqual     | 参数不等于比较值    |
| IsNotNull      | 参数不等于 null  |
| IsNull         | 参数等于 null    |
| IsProperty     | 查询对象包含属性名 |
| IsTrue         | 参数为 true         |
| Switch         | Switch 标签      |
| Case           | Switch标签的子标签,等于比较值 |
| Defalut        | Switch标签的子标签,未命中任何Case子标签时命中此标签  |

## Statement 其他子标签

| 标签       |    说明   |
| :--------- | --------:|
| Env        | 用于判断全局变量 DbProvider |
| Include    | 引用外部Statement     |
| Dynamic    | 动态标签,用于包裹筛选标签,匹配的第一个筛选标签的前缀将忽略 |
| For        | 用于参数为IEnumerable,遍历参数动态拼接Sql |
| Where      | 继承至Dynamic,用于包裹筛选标签,匹配的第一个筛选标签前缀被忽略,并添加 Where 前缀|
| Set      | 继承至Dynamic,用于Update,包裹筛选标签,匹配的第一个筛选标签前缀被忽略,并添加 Set 前缀,必须匹配至少一个子标签，否则将抛出SmartSqlException异常。|
| Placeholder    | 占位符标签，用于替换参数键值 |

### Demo

#### For

> 如果数组内类型里边为（值类型 | String）, Key属性值为必选，且与For标签内键值保持一致

``` csharp
    var list = SqlMapper.Query<T_Test>(new RequestContext
        {
            Scope = "T_Test",
            SqlId = "GetList",
            Request = new
            {
                LikeNames = new string[] { "Ahoo", "Good" }
            }
        });
```

``` xml
    <For Prepend="And" Property="LikeNames" Key="Name" Open="(" Separator="Or" Close=")">
        Name Like Concat('%',@Name,'%')
    </For>
```

> 如果数组内类型里边为 !（值类型 | String）

``` csharp
    IList<T_Test> test_list = new List<T_Test> {
        new T_Test{  Name="1", Status=1},
        new T_Test{  Name="2", Status=2}
    };
    SqlMapper.Execute(new RequestContext
        {
            Scope = "T_Test",
            SqlId = "InsertRange",
            Request = new { Values = test_list }
        });
```

``` xml
<Statement Id="InsertRange">
    INSERT INTO T_Test
    (Name,Status)
    VALUES
    <For Prepend="" Property="Values" Open="" Separator="," Close="">
        (@Name,@Status)
    </For>
</Statement>
```

---