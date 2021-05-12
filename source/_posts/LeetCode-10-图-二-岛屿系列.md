---
title: LeetCode-10-图-二-岛屿系列
date: 2020-02-17 14:55:49
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---

## 十、图

### 133.克隆图
    链接：https://leetcode-cn.com/problems/clone-graph/

    给你无向 连通 图中一个节点的引用，请你返回该图的 深拷贝（克隆）。

    图中的每个节点都包含它的值 val（int） 和其邻居的列表（list[Node]）。

    class Node {
        public int val;
        public List<Node> neighbors;
    }

题解一|dfs|hash：

思路：

1、使用一个哈希表存储所有已被访问和克隆的节点。哈希表中的 key 是原始图中的节点，value 是克隆图中的对应节点。

2、从给定节点开始遍历图。如果某个节点已经被访问过，则返回其克隆图中的对应节点。我们给定无向边边 A - B，表示 A 能连接到 B，且 B 能连接到 A。如果不对访问过的节点做标记，则会陷入死循环中。

3、如果当前访问的节点不在哈希表中，则创建它的克隆节点并存储在哈希表中。注意：在进入递归之前，必须先创建克隆节点并保存在哈希表中。如果不保证这种顺序，可能会在递归中再次遇到同一个节点，再次遍历该节点时，陷入死循环。

4、递归调用每个节点的邻接点。每个节点递归调用的次数等于邻接点的数量，每一次调用返回其对应邻接点的克隆节点，最终返回这些克隆邻接点的列表，将其放入对应克隆节点的邻接表中。这样就可以克隆给定的节点和其邻接点。


```
"""
# Definition for a Node.
class Node:
    def __init__(self, val = 0, neighbors = []):
        self.val = val
        self.neighbors = neighbors
"""
class Solution:
    def cloneGraph(self, node: 'Node') -> 'Node':
        lookup={}
        def dfs(node):
            # print(node.val)
            if not node:
                return
            if node in lookup:
                return lookup[node]
            clone=Node(node.val,[])
            lookup[node]=clone
            for n in node.neighbors:
                clone.neighbors.append(dfs(n))
            return clone
        return dfs(node)
```
题解二|bfs：

```
"""
# Definition for a Node.
class Node:
    def __init__(self, val = 0, neighbors = None):
        self.val = val
        self.neighbors = neighbors if neighbors is not None else []
"""
from collections import deque
class Solution:
    def cloneGraph(self, node: 'Node') -> 'Node':
        if not node:
            return
        visited={}
        queue=deque([node])
        visited[node]=Node(node.val,[])
        while queue:
            n=queue.popleft()
            for neighbor in n.neighbors:
                if neighbor not in visited:
                    visited[neighbor]=Node(neighbor.val,[])
                    queue.append(neighbor)
                visited[n].neighbors.append(visited[neighbor]) # 更新当前节点的邻居列表
        return visited[node]
```
### 200. 岛屿数量
    链接：https://leetcode-cn.com/problems/number-of-islands/

    给你一个由 '1'（陆地）和 '0'（水）组成的的二维网格，请你计算网格中岛屿的数量。

    岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。

    此外，你可以假设该网格的四条边均被水包围。

     

    示例 1：

    输入：grid = [
      ["1","1","1","1","0"],
      ["1","1","0","1","0"],
      ["1","1","0","0","0"],
      ["0","0","0","0","0"]
    ]
    输出：1
    示例 2：

    输入：grid = [
      ["1","1","0","0","0"],
      ["1","1","0","0","0"],
      ["0","0","1","0","0"],
      ["0","0","0","1","1"]
    ]
    输出：3
     

    提示：

    m == grid.length
    n == grid[i].length
    1 <= m, n <= 300
    grid[i][j] 的值为 '0' 或 '1'

题解一|DFS：

参考：https://leetcode-cn.com/problems/number-of-islands/solution/dao-yu-lei-wen-ti-de-tong-yong-jie-fa-dfs-bian-li-/

```
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        def helper(grid,i,j):
            if i<0 or i>=len(grid) or j<0 or j>=len(grid[0]) or grid[i][j]=='0' or grid[i][j]=='2': # 判断是否在网格中
                return
            grid[i][j]='2' # 避免重复遍历

            # 访问上、下、左、右四个相邻结点
            helper(grid,i-1,j) # 上
            helper(grid,i+1,j) # 下
            helper(grid,i,j-1) # 左
            helper(grid,i,j+1) # 右
        
        if not grid or len(grid[0])==0:
            return 0
        count=0
        for i in range(len(grid)):
            for j in range(len(grid[0])):
                if grid[i][j]=='1':
                    count+=1
                    helper(grid,i,j)
        return count
```

题解二|BFS|超时：
```
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        def bfs(grid,i,j):
            queue=[[i,j]]
            while queue:
                i,j=queue.pop(0)
                if 0 <= i < len(grid) and 0 <= j < len(grid[0]) and grid[i][j] =='1':
                    grid[i][j]=='2'
                    queue+=[[i+1,j],[i-1,j],[i,j+1],[i,j-1]]
        count=0
        for i in range(len(grid)):
            for j in range(len(grid[0])):
                # if grid[i][j]=='0' or grid[i][j]=='2':
                #     continue
                if grid[i][j]=='1':
                    bfs(grid,i,j)
                    count+=1
        return count
```

```
class Solution:
    def __init__(self) -> None:
        self.directions=[(1,0),(-1,0),(0,1),(0,-1)]

    def inArea(self,i,j,rows,cols):
        return i>=0 and i<rows and j>=0 and j<cols

    def numIslands(self, grid: List[List[str]]) -> int:
        if not grid or not grid[0]:
            return 0
            
        m,n=len(grid),len(grid[0])

        count=0

        for i in range(m):
            for j in range(n):
                if grid[i][j]=='1':
                    grid[i][j]=='2'
                    self.bfs(grid,i,j,m,n)
                    count+=1
        return count

    def bfs(self,grid,i,j,rows,cols):
        queue=[(i,j)]
        while queue:
            front=queue.pop(0)
            x=front[0]
            y=front[1]
            for direction in self.directions:
                newx=x+direction[0]
                newy=y+direction[1]
                if self.inArea(newx,newy,rows,cols) and grid[newx][newy]=='1':
                    grid[newx][newy]='2'
                    queue.append((newx,newy))
        return True
```


### 463. 岛屿的周长
    链接：https://leetcode-cn.com/problems/island-perimeter/

    给定一个包含 0 和 1 的二维网格地图，其中 1 表示陆地 0 表示水域。

    网格中的格子水平和垂直方向相连（对角线方向不相连）。整个网格被水完全包围，但其中恰好有一个岛屿（或者说，一个或多个表示陆地的格子相连组成的岛屿）。

    岛屿中没有“湖”（“湖” 指水域在岛屿内部且不和岛屿周围的水相连）。格子是边长为 1 的正方形。网格为长方形，且宽度和高度均不超过 100 。计算这个岛屿的周长。

    示例 :

    输入:
    [[0,1,0,0],
     [1,1,1,0],
     [0,1,0,0],
     [1,1,0,0]]

    输出: 16

    解释: 它的周长是下面图片中的 16 个黄色的边：

题解一|DFS：

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/grid.jpg](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/grid.jpg)

当我们的 dfs 函数因为「坐标 (r, c) 超出网格范围」返回的时候，实际上就经过了一条黄色的边；而当函数因为「当前格子是海洋格子」返回的时候，实际上就经过了一条蓝色的边。

```
class Solution:
    def islandPerimeter(self, grid: List[List[int]]) -> int:

        def dfs(grid,i,j):
            # if i<0 or i>=len(grid) or j<0 or j>=len(grid[0]):
            #     return 1
            if not (0 <= i < m and 0 <= j < n):
                return 1
            if grid[i][j]==0:
                return 1
            if grid[i][j]!=1:
                return 0
            grid[i][j]=2
            return dfs(grid,i-1,j)+dfs(grid,i+1,j)+dfs(grid,i,j-1)+dfs(grid,i,j+1)

        if not grid or len(grid[0])==0:
            return 0
        m=len(grid)
        n=len(grid[0])
        for x in range(m):
            for y in range(n):
                if grid[x][y]==1:
                    return dfs(grid,x,y)
```

### 695. 岛屿的最大面积
    链接：https://leetcode-cn.com/problems/max-area-of-island/

    给定一个包含了一些 0 和 1 的非空二维数组 grid 。

    一个 岛屿 是由一些相邻的 1 (代表土地) 构成的组合，这里的「相邻」要求两个 1 必须在水平或者竖直方向上相邻。你可以假设 grid 的四个边缘都被 0（代表水）包围着。

    找到给定的二维数组中最大的岛屿面积。(如果没有岛屿，则返回面积为 0 。)

    示例 1:

    [[0,0,1,0,0,0,0,1,0,0,0,0,0],
     [0,0,0,0,0,0,0,1,1,1,0,0,0],
     [0,1,1,0,1,0,0,0,0,0,0,0,0],
     [0,1,0,0,1,1,0,0,1,0,1,0,0],
     [0,1,0,0,1,1,0,0,1,1,1,0,0],
     [0,0,0,0,0,0,0,0,0,0,1,0,0],
     [0,0,0,0,0,0,0,1,1,1,0,0,0],
     [0,0,0,0,0,0,0,1,1,0,0,0,0]]
    对于上面这个给定矩阵应返回 6。注意答案不应该是 11 ，因为岛屿只能包含水平或垂直的四个方向的 1 。

    示例 2:

    [[0,0,0,0,0,0,0,0]]
    对于上面这个给定的矩阵, 返回 0。

    注意: 给定的矩阵grid 的长度和宽度都不超过 50。

题解一|DFS：
```
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        def dfs(grid,i,j):
            if not (0<=i<m and 0<=j<n):
                return 0
            if grid[i][j]!=1: # 注意这样判断，才能递归走到dfs
                return 0
            grid[i][j]=2
            return 1+dfs(grid,i+1,j)+dfs(grid,i-1,j)+dfs(grid,i,j+1)+dfs(grid,i,j-1)
            
        m=len(grid)
        n=len(grid[0])
        area=0
        if not grid or not n:
            return 0
        for i in range(m):
            for j in range(n):
                if grid[i][j]==1:
                    tmp=dfs(grid,i,j)
                    area=max(area,tmp)
        return area
```

题解二|bfs：

时间复杂度：O(m*n)，这里 m 表示二维矩阵的行数、n 表示二维矩阵的列数，最坏情况下每一个单元格都会被遍历一次；
空间复杂度：O(m)*n，数组 visited 的大小为 
m×n，队列的大小最多为 m*n。

```
class Solution:
    def __init__(self) -> None:
        self.directions=[(0,1),(0,-1),(1,0),(-1,0)]

    def inArea(self,i,j,rows,cols):
        return i>=0 and i<rows and j>=0 and j<cols

    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        m,n=len(grid),len(grid[0])
        visited=[[False]*n for i in range(m)]
        maxArea=0
        for i in range(m):
            for j in range(n):
                if grid[i][j]==1 and not visited[i][j]:
                    maxArea=max(maxArea,self.bfs(grid,i,j,m,n,visited))
        return maxArea
    
    def bfs(self,grid,i,j,rows,cols,visited):
        count=0
        queue=[(i,j)]
        visited[i][j]=True
        while queue:
            front=queue.pop(0)
            x=front[0]
            y=front[1]
            count+=1
            for direction in self.directions:
                newx=x+direction[0]
                newy=y+direction[1]

                if self.inArea(newx,newy,rows,cols) and grid[newx][newy] == 1 and not visited[newx][newy]:
                    queue.append((newx,newy))
                    visited[newx][newy]=True
        return count
```

转换为一维向量：

```
class Solution:
    def __init__(self) -> None:
        self.directions=[(0,1),(0,-1),(1,0),(-1,0)]

    def inArea(self,i,j,rows,cols):
        return i>=0 and i<rows and j>=0 and j<cols

    def getIndex(self,x,y,cols):
        return x*cols+y

    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        m,n=len(grid),len(grid[0])
        visited=[False]* (m*n)
        maxArea=0
        for i in range(m):
            for j in range(n):
                if grid[i][j]==1 and not visited[self.getIndex(i,j,n)]:
                    maxArea=max(maxArea,self.bfs(grid,i,j,m,n,visited))
        return maxArea
    
    def bfs(self,grid,i,j,rows,cols,visited):
        count=0
        index=self.getIndex(i,j,cols)
        queue=[index]
        visited[index]=True
        while queue:
            front=queue.pop(0)
            x=front//cols
            y=front%cols
            count+=1
            for direction in self.directions:
                newx=x+direction[0]
                newy=y+direction[1]
                newindex=self.getIndex(newx,newy,cols)

                if self.inArea(newx,newy,rows,cols) and grid[newx][newy] == 1 and not visited[newindex]:
                    queue.append(newindex)
                    visited[newindex]=True
        return count
```

### 827. 最大人工岛
    链接：https://leetcode-cn.com/problems/making-a-large-island/

    在二维地图上， 0代表海洋， 1代表陆地，我们最多只能将一格 0 海洋变成 1变成陆地。

    进行填海之后，地图上最大的岛屿面积是多少？（上、下、左、右四个方向相连的 1 可形成岛屿）

    示例 1:

    输入: [[1, 0], [0, 1]]
    输出: 3
    解释: 将一格0变成1，最终连通两个小岛得到面积为 3 的岛屿。
    示例 2:

    输入: [[1, 1], [1, 0]]
    输出: 4
    解释: 将一格0变成1，岛屿的面积扩大为 4。
    示例 3:

    输入: [[1, 1], [1, 1]]
    输出: 4
    解释: 没有0可以让我们变成1，面积依然为 4。
    说明:

    1 <= grid.length = grid[0].length <= 50
    0 <= grid[i][j] <= 1

题解一|DFS：

参考：https://leetcode-cn.com/problems/number-of-islands/solution/dao-yu-lei-wen-ti-de-tong-yong-jie-fa-dfs-bian-li-/

```
class Solution:
    def largestIsland(self, grid: List[List[int]]) -> int:
        def inArea(grid,i,j):
            return (0<=i<m and 0<=j<n)

        def area(grid,i,j,value): # value代表当前岛屿的编号,求得单个岛屿的面积
            if not inArea(grid,i,j):
                return 0
            if grid[i][j]!=1:
                return 0
            grid[i][j]=value
            return 1+area(grid,i+1,j,value)+area(grid,i-1,j,value)+area(grid,i,j+1,value)+area(grid,i,j-1,value)

        def thisArea(grid,i,j,hash): # 将(i,j)填海后，最大岛屿的面积
            if grid[i][j]!=0:
                return hash[grid[i][j]]
            res=0
            adjs=set()
            if inArea(grid,i-1,j) and grid[i-1][j]>0: # 格子不越界，格子是陆地
                adjs.add(grid[i-1][j])
            if inArea(grid,i+1,j) and grid[i+1][j]>0:
                adjs.add(grid[i+1][j])
            if inArea(grid,i,j-1) and grid[i][j-1]>0:
                adjs.add(grid[i][j-1])
            if inArea(grid,i,j+1) and grid[i][j+1]>0:
                adjs.add(grid[i][j+1])

            for each in adjs:
                res+=hash[each]
            return res+1

        m,n=len(grid),len(grid[0])
        value=2
        hash={}
        res=0

        for i in range(m):
            for j in range(n):
                if grid[i][j]==1:
                    tmp=area(grid,i,j,value)
                    hash[value]=tmp
                    value+=1

        for i in range(m): # DFS 遍历海洋格子，观察每个海洋格子相邻的陆地格子。
            for j in range(n):
                tmp=thisArea(grid,i,j,hash) 
                res=max(res,tmp)
        return res
```


### 1254. 统计封闭岛屿的数目
    链接：https://leetcode-cn.com/problems/number-of-closed-islands/

    有一个二维矩阵 grid ，每个位置要么是陆地（记号为 0 ）要么是水域（记号为 1 ）。

    我们从一块陆地出发，每次可以往上下左右 4 个方向相邻区域走，能走到的所有陆地区域，我们将其称为一座「岛屿」。

    如果一座岛屿 完全 由水域包围，即陆地边缘上下左右所有相邻区域都是水域，那么我们将其称为 「封闭岛屿」。

    请返回封闭岛屿的数目。

    示例 1：

    输入：grid = [[1,1,1,1,1,1,1,0],[1,0,0,0,0,1,1,0],[1,0,1,0,1,1,1,0],[1,0,0,0,0,1,0,1],[1,1,1,1,1,1,1,0]]
    输出：2
    解释：
    灰色区域的岛屿是封闭岛屿，因为这座岛屿完全被水域包围（即被 1 区域包围）。
    示例 2：

    输入：grid = [[0,0,1,0,0],[0,1,0,1,0],[0,1,1,1,0]]
    输出：1
    示例 3：

    输入：grid = [[1,1,1,1,1,1,1],
                [1,0,0,0,0,0,1],
                [1,0,1,1,1,0,1],
                [1,0,1,0,1,0,1],
                [1,0,1,1,1,0,1],
                [1,0,0,0,0,0,1],
                [1,1,1,1,1,1,1]]
    输出：2
    
    提示：

    1 <= grid.length, grid[0].length <= 100
    0 <= grid[i][j] <=1

题解一|dfs：
```
class Solution:
    def __init__(self):
        self.val=1

    def closedIsland(self, grid: List[List[int]]) -> int:
        def dfs(grid,i,j):
            if i<0 or i>=m or j<0 or j>=n:
                self.val=0
                return
            if grid[i][j] != 0:
                return
            grid[i][j]=2
            dfs(grid,i+1,j)
            dfs(grid,i-1,j)
            dfs(grid,i,j+1)                
            dfs(grid,i,j-1)


        if not grid or len(grid[0])==0:
            return 0
        m,n=len(grid),len(grid[0])
        count=0
        for i in range(m):
            for j in range(n):
                if grid[i][j]==0:
                    self.val=1
                    dfs(grid,i,j)
                    count+=self.val
        return count
```

```
class Solution:
    def closedIsland(self, grid: List[List[int]]) -> int:
        def dfs(grid,i,j):
            if i<0 or i>=m or j<0 or j>=n:
                return False # 到达边界，非封闭
            if grid[i][j] != 0:
                return True
            grid[i][j]=2
            up = dfs(grid,i-1,j);
            down = dfs(grid,i+1,j);
            left = dfs(grid,i,j-1);
            right = dfs(grid,i,j+1);
            if up and down and left and right:
                return True
            # return dfs(grid,i-1,j) && dfs(grid,i+1,j) && dfs(grid,i,j-1) && dfs(grid,i,j+1) 注意：此处改成这样子运行不能全部case，原因是在这里作为return返回，会被截断。
            return False

        if not grid or len(grid[0])==0:
            return 0
        m,n=len(grid),len(grid[0])
        count=0
        for i in range(m):
            for j in range(n):
                if grid[i][j]==0:
                    if dfs(grid,i,j):
                        count+=1
        return count
```

```
class Solution:
    def closedIsland(self, grid: List[List[int]]) -> int:
        def dfs(grid,i,j):
            if i<0 or i>=m or j<0 or j>=n:
                return True # 到达边界，非封闭
            if grid[i][j] != 0:
                return False
            grid[i][j]=2
            up = dfs(grid,i-1,j);
            down = dfs(grid,i+1,j);
            left = dfs(grid,i,j-1);
            right = dfs(grid,i,j+1);
            
            # return dfs(grid,i-1,j) or dfs(grid,i+1,j) or dfs(grid,i,j-1) or dfs(grid,i,j+1)
            return up or down or left or right

        if not grid or len(grid[0])==0:
            return 0
        m,n=len(grid),len(grid[0])
        count=0
        for i in range(m):
            for j in range(n):
                if grid[i][j]==0:
                    if not dfs(grid,i,j):
                        count+=1
        return count
```

### 1568. 使陆地分离的最少天数
    链接：https://leetcode-cn.com/problems/minimum-number-of-days-to-disconnect-island

    给你一个由若干 0 和 1 组成的二维网格 grid ，其中 0 表示水，而 1 表示陆地。岛屿由水平方向或竖直方向上相邻的 1 （陆地）连接形成。

    如果 恰好只有一座岛屿 ，则认为陆地是 连通的 ；否则，陆地就是 分离的 。

    一天内，可以将任何单个陆地单元（1）更改为水单元（0）。

    返回使陆地分离的最少天数。

    示例 1：

    输入：grid = [[0,1,1,0],[0,1,1,0],[0,0,0,0]]
    输出：2
    解释：至少需要 2 天才能得到分离的陆地。
    将陆地 grid[1][1] 和 grid[0][2] 更改为水，得到两个分离的岛屿。
    示例 2：

    输入：grid = [[1,1]]
    输出：2
    解释：如果网格中都是水，也认为是分离的 ([[1,1]] -> [[0,0]])，0 岛屿。
    示例 3：

    输入：grid = [[1,0,1,0]]
    输出：0
    示例 4：

    输入：grid = [[1,1,0,1,1],
                [1,1,1,1,1],
                [1,1,0,1,1],
                [1,1,0,1,1]]
    输出：1
    示例 5：

    输入：grid = [[1,1,0,1,1],
                [1,1,1,1,1],
                [1,1,0,1,1],
                [1,1,1,1,1]]
    输出：2
    

    提示：

    1 <= grid.length, grid[i].length <= 30
    grid[i][j] 为 0 或 1

题解一|dfs:
```
class Solution:
    def minDays(self, grid: List[List[int]]) -> int:
        def dfs(grid,i,j):
            if i<0 or i>=m or j<0 or j>=n or grid[i][j]==0 or grid[i][j]==2:
                return
            grid[i][j]=2
            dfs(grid,i+1,j)
            dfs(grid,i-1,j)
            dfs(grid,i,j+1)
            dfs(grid,i,j-1)

        def count():
            cnt=0
            for i in range(m):
                for j in range(n):
                    if grid[i][j]==1:
                        cnt+=1
                        dfs(grid,i,j)
            # 还原
            for i in range(m):
                for j in range(n):
                    if grid[i][j]==2:
                        grid[i][j]=1
            return cnt
        m,n=len(grid),len(grid[0])

        if count() !=1:
            return 0 # 如果岛屿个数已经大于1个，或者为0个，得出不需要分离。
        for i in range(m):
            for j in range(n):
                if grid[i][j]:
                    grid[i][j]=0
                    if count() != 1: # 分隔1个岛屿，如果岛屿个数已经大于1个，或者为0个，得出需要分离1个。
                        return 1
                    grid[i][j]=1
        return 2 # 如果以上都没有返回，说明需要分隔2个。因为任意连通岛屿通过分隔2次，都能分隔成功。
```