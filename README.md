#PHP 项目规范

---

#####框架推荐:
PHP框架功能大致为:
[路由, 数据库, 中间件, 触发器, 辅助功能]

建议选择轻型框架, Debug成本低

1. 路由
路由放进统一的router文件, 避免使用方法名路由

2. 数据库
尽量自己写Query

3. 中间件
把公共的判断放入中间件, 可以在路由曾实现, 可以在base controller实现

4. 辅助功能

5. 触发器

---

#####代码结构

[Controllers, Services, Models]
这三个模块分别作用是IO, 业务逻辑, 数据处理
######Controllers
在Controller中只做input\output, 因为公共参数验证在中间件中已经验证过了, 拿到input后不需要做验证除非有独特的参数验证, 或者参数后把参数放入Service里, Servicec处理逻辑后将返回值传给Controller
######Services
Service中是处理大量逻辑的地方, input是从Controller中获得的, 处理的数据是从Model中获得的, 这里是大量放入if判断和for循环的地方
######Models
所有数据服务都放在model层, 不管是访问redis还是mysql都在model层去操作, 关于mysql的Query, 尽量手写, 到时候很容易把其中的Query抽离出来作分析

---
#####关于PHP瓶颈

从以往的经验中理解, 从项目角度出发, PHP的瓶颈大部分集中在服务中, mysql的读写, 理想情况下, 数据的读取一般是从缓存获取, 个别情况从数据库中获取数据. 所有写入数据都用走异步服务.

---
#####书写习惯
1. Namespace
一般来说一个模块一个命名空间, Controller, Service, Model, Library(共用方法) 调用方法时直接 
`\Services\SomeClass::function()`

2. 所有东西起名规则
`Namesapces` : 开头大写 + 驼峰
`Classes`    : 开头大写 + 驼峰
`Functions`  : 开头小写 + 驼峰  
`variables`  : 小写 + 下划线 + 小写
`riviate_variables` : 开头下划线 + 小写
`CONSTENT`   : 全大写 + 下划线

3. 方法返回值
所有方法返回值尽量不用单一返回值
最好说明方法返回情况
`[code, data, msg]`

4. 逻辑判断(重要)
尽量不用多重判断
```
if($condi_1 == true)
{
    if($condi_2 == false)
    {
        func();
    }
    else
    {
        return;
    }
}
else
{
    return;
}
```
上方代码可以优化为
```
if($condi_1 == false)
    return;
if($condi_2 == true)
    return;
else
    func();

```
先判断返回情况增加可读性

5. 用do while分割方法
```
function()
{
    $return = [];
    do
    {
        if($condi_1 == false)
        {
            $return = ['msg'='condi_1 is false'];
            break;
        }
        else($cond_2 == true)
        {
            $return = ['msg'='condi_2 is true'];
            break;
        }
    }while(false);

    general();//通用处理

    return $return;
}
```
这种写法有两点好处 1. 有通用处理 2. 将大方法分割成小方法
这种写法对于逻辑复制的方法好用

6. 中间件

7. 日志