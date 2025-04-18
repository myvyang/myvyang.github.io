---
title: "python的标准库中简单好用的工具"
date: 2025-04-18
---

## Counter

```python

from collections import Counter

counter = Counter(['apple', 'banana', 'apple', 'orange', 'banana', 'apple'])

# 直接计数
print(counter)
# 输出: Counter({'apple': 3, 'banana': 2, 'orange': 1})

# 简单统计topN
print(counter.most_common(2))
# 输出: [('apple', 3), ('banana', 2)]

# 字符串也可以计数
counter = Counter("hello world")
print(counter)
# 输出: Counter({'l': 3, 'o': 2, 'h': 1, 'e': 1, ' ': 1, 'w': 1, 'r': 1, 'd': 1})

c1 = Counter(['apple', 'banana', 'apple'])
c2 = Counter(['banana', 'orange'])

# 集合操作: 交集（取最小值）
print(c1 & c2)
# 输出: Counter({'banana': 1})
```

## namedtuple

```python
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])
p = Point(10, 20)

print(p.x, p.y)  
# 输出: 10 20

print(p)  
# 输出: Point(x=10, y=20)
```

## defaultdict

可以创建递归的嵌套dict

```python
from collections import defaultdict

# 创建一个嵌套字典
nested_dict = lambda: defaultdict(nested_dict)
dd = nested_dict()
dd['a']['b']['c'] = 1

print(dd)  # 输出: defaultdict(<function <lambda> at ...>, {'a': defaultdict(..., {'b': defaultdict(..., {'c': 1})})})
```

## 无限序列

```python
import itertools

# 生成从 10 开始，步长为 2 的数列, 无限增长
counter = itertools.count(10, 2)
for i in range(5):
    print(next(counter))
# 输出: 10, 12, 14, 16, 18

# 循环遍历 ['a', 'b', 'c'], 无限循环
cycler = itertools.cycle(['a', 'b', 'c'])
for i in range(5):
    print(next(cycler))
# 输出: a, b, c, a, b

# 有限次重复, 不带数量则无限重复
for item in itertools.repeat('world', 3):
    print(item)
# 输出: world, world, world

```

## 笛卡尔积

```python
import itertools

# 笛卡尔积
for p in itertools.product([1, 2], ['a', 'b']):
    print(p)
# 输出: (1, 'a'), (1, 'b'), (2, 'a'), (2, 'b')

# 带重复的笛卡尔积
for p in itertools.product([1, 2], repeat=2):
    print(p)
# 输出: (1, 1), (1, 2), (2, 1), (2, 2)
```

## 排列生成

`itertools.combinations`和`itertools.permutations`的唯一区别是, `combinations`不允许生成的tuple包含重复元素.

```python
import itertools

# 全排列
for p in itertools.permutations([1, 2, 3]):
    print(p)
# 输出: (1, 2, 3), (1, 3, 2), (2, 1, 3), (2, 3, 1), (3, 1, 2), (3, 2, 1)

# 指定长度的排列
for p in itertools.permutations([1, 2, 3], 2):
    print(p)
# 输出: (1, 2), (1, 3), (2, 1), (2, 3), (3, 1), (3, 2)

# 组合
for c in itertools.combinations([1, 2, 3], 2):
    print(c)
# 输出: (1, 2), (1, 3), (2, 3)
```

`itertools.combinations`和`itertools.permutations`的唯一区别是, `combinations`输出的是无序组合, 结果是`permutations`的子集.
```

