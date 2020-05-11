# 合并排序

## 分治算法

A:[45,63,98,75,62,10,63]

SA=Merge_Sort(A)

问题分解，把输入切分

A分为LA，RA

LA ∪ RA = A

P(A -> SA)

于是有P1 = LA -> SLA，P2 = RA -> SRA，

SLA = Merge_Sort(LA)	P1

SRA = Merge_Sort(RA)	P2

合并(SLA,SRA) = SA

![image-20200303135817257](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200303135817257.png)

```python
def merge_sort(A):
    if len(A) <= 1:     #'''边界元素，当序列A中只有一个元素时返回序列A'''
        return A
    mid = len(A)//2     #'''索引，指向中间元素'''
    leftA = A[:mid]
    rightA = A[mid:]

    sort_leftA = merge_sort(leftA)
    sort_rightA = merge_sort(rightA)

    sortA = merge(sort_leftA,sort_rightA)
    return sortA


def merge(sLA,sRA):
    i,j = 0,0
    alist = []
    while i < len(sLA) and j < len(sRA):
        if sLA[i] < sRA[j]:
            alist.append(sLA[i])
            i += 1
        else:
            alist.append(sRA[j])
            j += 1

    while i < len(sLA):         #i,j排序完后剩下元素添加
        alist.append(sLA[i])
        i += 1
    while j < len(sRA):
        alist.append(sRA[j])
        j += 1
    return alist


# lA = [23,45,89,66]
# rA = [16,21,32,56]
#
# print(merge(lA,rA))
A = [54,26,17,77,31,44,20,1]
print(merge_sort(A))
```

# 广度优先搜索(BFS)

从根到叶，从左到右

队列

```shell
>>>python
>>>graph = {
    "A":["B","C"],
    "B":["A","C","D"],
    "C":["A","B","D","E"],
    "D":["B","C","E","F"],
    "E":["C","D"],
    "F":["D"]
}
>>> graph.keys()
dict_keys(['A', 'B', 'C', 'D', 'E', 'F'])
>>> graph["E"]
['C', 'D']
>>> queue = []
>>> queue.append('A')
>>> queue.append('B')
>>> queue.append('C')
>>> queue
['A', 'B', 'C']
>>> queue.pop(0)
'A'
>>> queue
['B', 'C']
>>>
```

``` python
graph = {
    "A":["B","C"],
    "B":["A","C","D"],
    "C":["A","B","D","E"],
    "D":["B","C","E","F"],
    "E":["C","D"],
    "F":["D"]
}#字段
def BFS(graph,s):
    queue = []
    queue.append(s)
    seen = set()   #代表hash,set集合,表示见过的队列
    seen.add(s)
    parent = {s:None}   #父母节点，最开始节点无父母节点

    while (len(queue) > 0):
        vertex = queue.pop(0)   #从队列里面拿一个点出来
        nodes = graph[vertex]
        # print(nodes)
        for w in nodes:
            if w not in seen:
                queue.append(w)
                seen.add(w)
                parent[w] = vertex      #映射父母节点
        # print(vertex)
    return parent

parent = BFS(graph,"E")
print(parent)
# for key in parent:
#     print(key,parent[key])
#点E走到点B
v = 'B'
while v != None:	#知道v为父母节点停止
    print(v)	#打印
    v = parent[v]	#该点的父母节点赋值节点
```

## 腐烂的橘子

```python
class Solution:
    def orangesRotting(self, grid: List[List[int]]) -> int:
        row = len(grid)
        col = len(grid[0])
        time = 0
        search = [[1, 0], [-1, 0], [0, 1], [0, -1]] #搜索规则
        queue = [] # 模拟queue队列先进先出，而不用stack栈
        # 找腐烂橘子的坐标
        for i in range(row):
            for j in range(col):
                if grid[i][j] == 2 :
                    queue.append([i, j, 0])		#当有腐烂的橘子的时候加入队列
        # 开始腐烂
        while queue:
            x, y, time = queue.pop(0)	#推出腐烂队列，推出之后循环该点周围的新鲜水果
            for k in search:
                s_x = x + k[0]
                s_y = y + k[1]
                # 边界限制
                if (0 <= s_x < row) and (0 <= s_y <col) and grid[s_x][s_y] == 1:	#如果新鲜，腐烂他
                    grid[s_x][s_y] = 2 
                    queue.append([s_x, s_y, time + 1])	#该水果加入队列，时间+1
        # 查找是否存在新鲜橘子
        for f in grid:
            if 1 in f:
                return -1
        # 返回结果
        return time
```



## PriorityQueue

```shell
>>> import heapq
>>> pqueue = []
>>> heapq.heappush(pqueue,(1,'A'))
>>> heapq.heappush(pqueue,(7,'B'))
>>> heapq.heappush(pqueue,(3,'C'))
>>> heapq.heappush(pqueue,(6,'D'))
>>> heapq.heappush(pqueue,(2,'E'))
>>> heapq.heappop(pqueue)
(1, 'A')
>>> heapq.heappop(pqueue)
(2, 'E')
>>> heapq.heappop(pqueue)
(3, 'C')
>>> heapq.heappop(pqueue)
(6, 'D')
>>> heapq.heappop(pqueue)
(7, 'B')
>>> heapq.heappop(pqueue)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: index out of range
>>> heapq.heappush(pqueue,(1,'A'))
>>> heapq.heappush(pqueue,(7,'B'))
>>> heapq.heappush(pqueue,(3,'C'))
>>> heapq.heappush(pqueue,(6,'D'))
>>> heapq.heappush(pqueue,(2,'E'))
>>> pqueue
[(1, 'A'), (2, 'E'), (3, 'C'), (7, 'B'), (6, 'D')]
```

## dijkstra

```shell
>>> graph = {
...     "A":{"B":5,"C":1},
...     "B":{"A":5,"C":2,"D":11},
...     "C":{"A":1,"B":2,"D":4,"E":8},
...     "D":{"B":1,"C":4,"E":3,"F":6},
...     "E":{"C":8,"D":3},
...     "F":{"D":6}
... }
>>> graph[]
  File "<stdin>", line 1
    graph[]
          ^
SyntaxError: invalid syntax
>>> graph["A"]
{'B': 5, 'C': 1}
>>> graph["A"].keys()
dict_keys(['B', 'C'])
>>> graph["A"]["B"]
5
```



# 深度优先搜索(DFS)

栈，回溯