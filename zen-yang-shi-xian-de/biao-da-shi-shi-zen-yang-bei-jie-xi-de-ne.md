# 表达式是怎样被解析的呢

## 原理

1. 将表达式进行过滤，替换字符等预处理成可以识别的格式
2. 将格式字符串生成抽象语法树AST
3. 遍历这颗树，执行对应的解析方法

## 代码实现

```python
# 目标表达式
# a + 3 * b

# 类型的定义
# 数字类型
Num = lambda env, n: n 
# 变量类型
Var = lambda env, x: env[x] 
# 加号类型
Add = lambda env, a, b: _eval(env, a) + _eval(env, b) 
# 乘号类型
Mul = lambda env, a, b: _eval(env, a) * _eval(env, b) 
 
 
# 解析AST树的方法
_eval = lambda env, expr: expr[0](env, *expr[1:]) 
 
# 传入的变量 
env = {'a':2, 'b':5} 
# AST语法树
tree = (Add, (Var, 'a'), 
       (Mul, (Num, 3), 
          (Var, 'b'))) 

# 解析并得出结果，17 
print(_eval(env, tree))
```

