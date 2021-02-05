---
title: LeetCode-9-回溯法
date: 2020-01-21 17:20:06
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---

参考文章：https://www.cnblogs.com/xiaomangxie/p/10208441.html
参考文章：https://leetcode-cn.com/problems/permutations/solution/hui-su-suan-fa-xiang-jie-by-labuladong-2/

## 九、回溯法

回溯法（back tracking）（探索与回溯法）是一种选优搜索法，又称为试探法，按选优条件向前搜索，以达到目标。但当探索到某一步时，发现原先选择并不优或达不到目标，就退回一步重新选择，这种走不通就退回再走的技术为回溯法，而满足回溯条件的某个状态的点称为“回溯点”。

解决一个回溯问题，实际上就是一个决策树的遍历过程。你只需要思考 3 个问题：

    1、路径：也就是已经做出的选择。

    2、选择列表：也就是你当前可以做的选择。

    3、结束条件：也就是到达决策树底层，无法再做选择的条件。

回溯算法的框架: 其核心就是 for 循环里面的递归，在递归调用之前「做选择」，在递归调用之后「撤销选择」。

```
result = []
def backtrack(路径, 选择列表):
    if 满足结束条件:
        result.add(路径)
        return
    
    for 选择 in 选择列表:
        做选择
        backtrack(路径, 选择列表)
        撤销选择
```
    
    「路径」，记录你已经做过的选择；
    「选择列表」，表示你当前可以做出的选择；
    「结束条件」就是遍历到树的底层，在这里就是选择列表为空的时候。

```
for 选择 in 选择列表:
    # 做选择
    将该选择从选择列表移除
    路径.add(选择)
    backtrack(路径, 选择列表)
    # 撤销选择
    路径.remove(选择)
    将该选择再加入选择列表
```

### 17. 电话号码的字母组合
    链接：https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/

    给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。

    给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

    示例:

    输入："23"
    输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
    说明:
    尽管上面的答案是按字典序排列的，但是你可以任意选择答案输出的顺序。

题解一|暴力：

时间复杂度：O(3^M×4^N)，M 是对应三个字母的数字个数，N 是对应四个字母的数字个数。
空间复杂度：O(3^M×4^N)，一共要生成 3^M×4^N个结果。

```
class Solution:
    def letterCombinations(self, digits: str) -> List[str]:
        key_all={
            '2': ['a', 'b', 'c'],
            '3': ['d', 'e', 'f'],
            '4': ['g', 'h', 'i'],
            '5': ['j', 'k', 'l'],
            '6': ['m', 'n', 'o'],
            '7': ['p', 'q', 'r', 's'],
            '8': ['t', 'u', 'v'],
            '9': ['w', 'x', 'y', 'z']
        }
        if digits=='':
            return ''
        ans=['']
        for num in digits:
            ans=[pre+suf for pre in ans for suf in key_all[num]]
        return ans
```

```
class Solution:
    def letterCombinations(self, digits: str) -> List[str]:
        if not digits:
            return []
        hash={
            '2': ['a', 'b', 'c'],
            '3': ['d', 'e', 'f'],
            '4': ['g', 'h', 'i'],
            '5': ['j', 'k', 'l'],
            '6': ['m', 'n', 'o'],
            '7': ['p', 'q', 'r', 's'],
            '8': ['t', 'u', 'v'],
            '9': ['w', 'x', 'y', 'z']
        }
        ans=['']
        for num in digits:
            tmp=[]
            for i in ans:
                for j in hash[num]:
                    tmp.append(i+j)
                ans=tmp[:]
        return ans
```

题解二|回溯：

时间复杂度：O(3^M×4^N)，M 是对应三个字母的数字个数，N 是对应四个字母的数字个数。
空间复杂度：O(3^M×4^N)，一共要生成 3^M×4^N个结果。

```
class Solution:
    def letterCombinations(self, digits: str) -> List[str]:
        if not digits:
            return []
        res=[]
        hash={
            1:'',
            2:'abc',
            3:'def',
            4:'ghi',
            5:'jkl',
            6:'mno',
            7:'pqrs',
            8:'tuv',
            9:'wxyz'
        }

        def traceback(digits,index,path):
            if index == len(digits):
                res.append(''.join(path))
                return
            
            digit=int(digits[index]) # hash的key定义是int
            for i in hash.get(digit,[]):
                path.append(i)
                traceback(digits,index+1,path)
                path.pop()
        traceback(digits,0,[])
        return res
```

```
class Solution:
    def letterCombinations(self, digits: str) -> List[str]:
        if not digits:
            return []
        res=[]
        hash={
            '1':'',
            '2':'abc',
            '3':'def',
            '4':'ghi',
            '5':'jkl',
            '6':'mno',
            '7':'pqrs',
            '8':'tuv',
            '9':'wxyz'
        }
        def traceback(digits,first,path):
            if first==len(digits):
                res.append(''.join(path))
                return
            # digit=int(digits[first])
            digit=digits[first] # hash的key定义为str
            for i in hash.get(digit,[]):
                path.append(i)
                traceback(digits,first+1,path)
                path.pop()
        traceback(digits,0,[])
        return res
```

### 22. 括号生成
    链接：https://leetcode-cn.com/problems/generate-parentheses/

    数字 n 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且有效的括号组合。

    示例：

    输入：n = 3
    输出：[
           "((()))",
           "(()())",
           "(())()",
           "()(())",
           "()()()"
         ]

题解一|暴力：

时间复杂度：O(2^{2n}n)，对于 2^{2n}个序列中的每一个，我们用于建立和验证该序列的复杂度为 O(n)。

空间复杂度：O(n)，除了答案数组之外，我们所需要的空间取决于递归栈的深度，每一层递归函数需要 O(1) 的空间，最多递归 2n 层，因此空间复杂度为 O(n)。

```
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        def generate(A):
            if len(A) == 2*n:
                if valid(A):
                    ans.append("".join(A))
            else:
                A.append('(')
                generate(A)
                A.pop()
                A.append(')')
                generate(A)
                A.pop()

        def valid(A):
            bal = 0
            for c in A:
                if c == '(': bal += 1
                else: bal -= 1
                if bal < 0: return False
            return bal == 0

        ans = []
        generate([])
        return ans
```

题解二|回溯：

可以只在序列仍然保持有效时才添加 '(' or ')'，而不是像 方法一 那样每次添加。

我们可以通过跟踪到目前为止放置的左括号和右括号的数目来做到这一点，

如果左括号数量不大于 n，我们可以放一个左括号。如果右括号数量小于左括号的数量，我们可以放一个右括号。

时间复杂度：O(4^n/sqrt(n))，在回溯过程中，每个答案需要 O(n)O(n) 的时间复制到答案数组中。

空间复杂度：O(n)，除了答案数组之外，我们所需要的空间取决于递归栈的深度，每一层递归函数需要 O(1) 的空间，最多递归 2n 层，因此空间复杂度为 O(n)。

```
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        
        def backtrace(S,left,right):
            if len(S) == 2*n:
                res.append(''.join(S))
                return
            
            if left < n:
                S.append('(')
                backtrace(S,left+1,right)
                S.pop()
            if right < left:
                S.append(')')
                backtrace(S,left,right+1)
                S.pop()

        res = []
        backtrace([],0,0)
        return res
```

```
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        
        def backtrace(S,index):
            if len(S) == 2*n:
                if index == 0:
                    res.append(''.join(S))
                return
            
            if index < n:
                S.append('(')
                backtrace(S,index+1)
                S.pop()
            if index > 0:
                S.append(')')
                backtrace(S,index-1)
                S.pop()

        res = []
        backtrace([],0)
        return res
```

题解三|递归：
```
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        res=[]
        def helper(left,right,n,s):
            if left==n and right==n:
                res.append(s)
                return
            if left < n:
                helper(left+1,right,n,s+'(')
            if left > right:
            # if left > right and right < n:
                helper(left,right+1,n,s+')')

        helper(0,0,n,'')
        return res
```
### 37. 解数独
    链接：https://leetcode-cn.com/problems/sudoku-solver/

    编写一个程序，通过已填充的空格来解决数独问题。

    一个数独的解法需遵循如下规则：

    数字 1-9 在每一行只能出现一次。
    数字 1-9 在每一列只能出现一次。
    数字 1-9 在每一个以粗实线分隔的 3x3 宫内只能出现一次。
    空白格用 '.' 表示。

    一个数独。

    答案被标成红色。

    Note:

    给定的数独序列只包含数字 1-9 和字符 '.' 。
    你可以假设给定的数独只有唯一解。
    给定数独永远是 9x9 形式的。

题解一|回溯：

```
class Solution:
    def solveSudoku(self, board: List[List[str]]) -> None:
        """
        Do not return anything, modify board in-place instead.
        """
        def backtrace(board,i,j):
            m,n=9,9
            if j==n: # 走到9越界，进入下一行
                return backtrace(board,i+1,0)
            if i==m: # 走到最后一行，找到一个可行解
                return True
            if board[i][j] != '.': # 当前是预设数字，直接跳到下一个
                return backtrace(board,i,j+1)
            chars=[str(i) for i in range(1,10)]
            for char in chars:
                if not isValid(board,i,j,char): # 遇到不合法的数字，跳过
                    continue

                board[i][j]=char # 做选择
                if backtrace(board,i,j+1): # 如果找到一个可行解，立即结束
                    return True
                board[i][j]='.' # 撤销选择
            return False

        def isValid(board,r,c,char):
            for i in range(9):
                # 判断行是否存在重复
                if board[r][i] == char:
                    return False
                # 判断列是否存在重复
                if board[i][c] == char:
                    return False
                # 判断 3 * 3 方框是否存在重复
                if board[(r//3)*3+i//3][(c//3)*3+i%3] == char:
                    return False
            return True
        backtrace(board,0,0)
```

### 39. 组合总和
    链接：https://leetcode-cn.com/problems/combination-sum/

    给定一个无重复元素的数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。

    candidates 中的数字可以无限制重复被选取。

    说明：

    所有数字（包括 target）都是正整数。
    解集不能包含重复的组合。 
    示例 1:

    输入: candidates = [2,3,6,7], target = 7,
    所求解集为:
    [
      [7],
      [2,2,3]
    ]
    示例 2:

    输入: candidates = [2,3,5], target = 8,
    所求解集为:
    [
      [2,2,2,2],
      [2,3,3],
      [3,5]
    ]

题解一|回溯:

https://leetcode-cn.com/problems/combination-sum/solution/hui-su-suan-fa-jian-zhi-python-dai-ma-java-dai-m-2/

```
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        size=len(candidates)
        if size==0:
            return []
            
        candidates.sort()
        res=[]
        path=[]
        return self.helper(candidates,0,size,target,res,path)
    def helper(self,candidates,begin,size,target,res,path):
        if target==0:
            res.append(path[:])
        for i in range(begin,size):
            residue=target-candidates[i]
            if residue < 0:
                break # 前面已排序，所以才能用break，否则还是要用continue。
            path.append(candidates[i])
            self.helper(candidates,i,size,residue,res,path)
            path.pop()
        return res
```

```
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        res=[]
        def backtrace(candidates,start,path,target):
            if target==0:
                # res.append(path[:])
                # list2 = list1[:]则是把list1通过切片运算取得的新list对象绑定到list2上，产生了新list，名称和引用也不同，所以，修改其中一个，另一个不会变。
                res.append(path+[])
                return

            for i in range(start,len(candidates)):
                tmp=target-candidates[i]
                if tmp < 0:
                    continue
                path.append(candidates[i])
                backtrace(candidates,i,path,tmp) # 注意此处i的取值
                # backtrace(candidates,start,path,tmp) # 结果为[[2,2,3],[2,3,2],[3,2,2],[7]]
                path.pop()
        
        backtrace(candidates,0,[],target)
        return res
```
### 40.组合总和 II
    链接：https://leetcode-cn.com/problems/combination-sum-ii/

    给定一个数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。

    candidates 中的每个数字在每个组合中只能使用一次。

    说明：

    所有数字（包括目标数）都是正整数。
    解集不能包含重复的组合。 
    示例 1:

    输入: candidates = [10,1,2,7,6,1,5], target = 8,
    所求解集为:
    [
      [1, 7],
      [1, 2, 5],
      [2, 6],
      [1, 1, 6]
    ]
    示例 2:

    输入: candidates = [2,5,2,1,2], target = 5,
    所求解集为:
    [
      [1,2,2],
      [5]
    ]

这道题与上一问的区别在于：

第 39 题：candidates 中的数字可以无限制重复被选取。
第 40 题：candidates 中的每个数字在每个组合中只能使用一次。
编码的不同在于下一层递归的起始索引不一样。

第 39 题：还从候选数组的当前索引值开始。
第 40 题：从候选数组的当前索引值的下一位开始。
相同之处：解集不能包含重复的组合。

题解一|回溯:
https://leetcode-cn.com/problems/combination-sum-ii/solution/hui-su-suan-fa-jian-zhi-python-dai-ma-java-dai-m-3/
```
class Solution:
    def combinationSum2(self, candidates: List[int], target: int) -> List[List[int]]:
        size=len(candidates)
        if size==0:
            return []
            
        candidates.sort()
        res=[]
        path=[]
        return self.helper(candidates,0,size,target,res,path)
    def helper(self,candidates,begin,size,target,res,path):
        if target==0:
            res.append(path[:])
        for i in range(begin,size):
            residue=target-candidates[i]
            if residue < 0:
                break

            if i > begin and candidates[i-1] == candidates[i]:
                continue

            path.append(candidates[i])
            self.helper(candidates,i+1,size,residue,res,path)
            path.pop()
        return res
```


去重：无论是求组合/子集/排列，只要原数组中含有重复元素，通用一个去重方法：

    1.先排序，使相同元素相邻；
    2.在backtrack的for循环里：
        if(i>start && candidates[i]==candidates[i-1]) continue;

```
这个避免重复思想是在是太重要了。
这个方法最重要的作用是，可以让同一层级，不出现相同的元素。即
                  1
                 / \
                2   2  这种情况不会发生 但是却允许了不同层级之间的重复即：
               /     \
              5       5
                例2
                  1
                 /
                 2      这种情况确是允许的
               /
              2  
                
为何会有这种神奇的效果呢？
首先 i-1 == i 是用于判定当前元素是否和之前元素相同的语句。这个语句就能砍掉例1。
可是问题来了，如果把所有当前与之前一个元素相同的都砍掉，那么例二的情况也会消失。 
因为当第二个2出现的时候，他就和前一个2相同了。
                
那么如何保留例2呢？
那么就用i > start 来避免这种情况，你发现例1中的两个2是处在同一个层级上的，
例2的两个2是处在不同层级上的。
在一个for循环中，所有被遍历到的数都是属于一个层级的。我们要让一个层级中，
必须出现且只出现一个2，那么就放过第一个出现重复的2，但不放过后面出现的2。
第一个出现的2的特点就是 i == start. 第二个出现的2 特点是i > start.

再通俗点，就是有重复元素的话，比如测试用例中的第一个1和第二个1，都会有1,7组成8，这样就产生了重复的list。因为都是从当前数开始遍历，所以加这一层的意思就是过滤掉重复的数，但是第一个1依然能使用第二个1，而第二个1是失去了作用的。


1、给定的数组可能有重复的元素，先排序，使得重复的数字相邻，方便去重。

2、for 循环枚举出选项时，加入下面判断，从而忽略掉同一层重复的选项，避免产生重复的组合。比如[1,2,2,2,5]中，选了第一个 2，变成 [1,2]，它的下一选项也是 2，跳过它，因为选它，就还是 [1,2]。


    if (i - 1 >= start && candidates[i - 1] == candidates[i]) {
        continue;
    }
    
3、当前选择的数字不能和下一个选择的数字重复，给子递归传i+1，避免与当前选的i重复。

    dfs(i + 1, temp, sum + candidates[i]);


```

```
class Solution:
    def combinationSum2(self, candidates: List[int], target: int) -> List[List[int]]:
        if not candidates:
            return []
        candidates.sort()
        res=[]

        def backtrace(candidates,start,path,target):
            if target==0:
                return res.append(path[:])
            for i in range(start,len(candidates)):
                tmp=target-candidates[i]
                if tmp < 0:
                    continue
                if i > start and candidates[i] == candidates[i-1]:
                    continue
                path.append(candidates[i])
                backtrace(candidates,i+1,path,tmp) # 此处是i+1
                path.pop()
        
        backtrace(candidates,0,[],target)
        return res
```
### 46.全排列
    链接：https://leetcode-cn.com/problems/permutations/

    给定一个没有重复数字的序列，返回其所有可能的全排列。

    示例:

    输入: [1,2,3]
    输出:
    [
      [1,2,3],
      [1,3,2],
      [2,1,3],
      [2,3,1],
      [3,1,2],
      [3,2,1]
    ]

题解一|回溯:
```
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        
        def backtrack(first=0):
            if first==lens:
                output.append(nums[:]) # 可以加上return
            for i in range(first,lens):
                nums[first],nums[i]=nums[i],nums[first]
                backtrack(first+1) # 注意：不能使用i+1，否则会出现[[1,2,3],[1,3,2],[2,1,3],[3,2,1]]
                nums[first],nums[i]=nums[i],nums[first]
        
        lens=len(nums)
        output=[]
        backtrack()
        return output
```

```
class Solution:
    def permute(self, nums):
        def backtrack(first=0):
            # if all integers are used up
            print('---------------first', first)
            if first == n:
                output.append(nums[:])
                print('output:',output)
            for i in range(first, n):
                # place i-th integer first
                # in the current permutation
                print('i,first:',i, first,nums[i], nums[first])
                nums[first], nums[i] = nums[i], nums[first]
                # use next integers to complete the permutations
                backtrack(first + 1)
                # backtrack
                nums[first], nums[i] = nums[i], nums[first]
                print('2:i,first:', i, first,nums[i], nums[first])

        n = len(nums)
        output = []
        backtrack()
        return output

输出：
---------------first 0
i,first: 0 0
1 1
---------------first 1
i,first: 1 1
2 2
---------------first 2
i,first: 2 2
3 3
---------------first 3
output: [[1, 2, 3]]
2:i,first: 2 2
2: 3 3
2:i,first: 1 1
2: 2 2
i,first: 2 1
3 2
---------------first 2
i,first: 2 2
2 2
---------------first 3
output: [[1, 2, 3], [1, 3, 2]]
2:i,first: 2 2
2: 2 2
2:i,first: 2 1
2: 3 2
2:i,first: 0 0
2: 1 1
i,first: 1 0
2 1
---------------first 1
i,first: 1 1
1 1
---------------first 2
i,first: 2 2
3 3
---------------first 3
output: [[1, 2, 3], [1, 3, 2], [2, 1, 3]]
2:i,first: 2 2
2: 3 3
2:i,first: 1 1
2: 1 1
i,first: 2 1
3 1
---------------first 2
i,first: 2 2
1 1
---------------first 3
output: [[1, 2, 3], [1, 3, 2], [2, 1, 3], [2, 3, 1]]
2:i,first: 2 2
2: 1 1
2:i,first: 2 1
2: 3 1
2:i,first: 1 0
2: 2 1
i,first: 2 0
3 1
---------------first 1
i,first: 1 1
2 2
---------------first 2
i,first: 2 2
1 1
---------------first 3
output: [[1, 2, 3], [1, 3, 2], [2, 1, 3], [2, 3, 1], [3, 2, 1]]
2:i,first: 2 2
2: 1 1
2:i,first: 1 1
2: 2 2
i,first: 2 1
1 2
---------------first 2
i,first: 2 2
2 2
---------------first 3
output: [[1, 2, 3], [1, 3, 2], [2, 1, 3], [2, 3, 1], [3, 2, 1], [3, 1, 2]]
2:i,first: 2 2
2: 2 2
2:i,first: 2 1
2: 1 2
2:i,first: 2 0
2: 3 1
```

### 47. 全排列 II
    链接：https://leetcode-cn.com/problems/permutations-ii/

    给定一个可包含重复数字的序列，返回所有不重复的全排列。

    示例:

    输入: [1,1,2]
    输出:
    [
      [1,1,2],
      [1,2,1],
      [2,1,1]
    ]
题解一|回溯：
https://leetcode-cn.com/problems/permutations-ii/solution/hui-su-suan-fa-python-dai-ma-java-dai-ma-by-liwe-2/
```
class Solution:
    def permuteUnie(self, nums: List[int]) -> List[List[int]]:
        
        def backtrack(first=0):
            if first==lens:
                output.append(nums[:])
            for i in range(first,lens):
                if not duplicat(nums,first,i):
                    continue
                nums[first],nums[i]=nums[i],nums[first]
                backtrack(first+1) # 注意：不能使用i+1，否则会出现[[1,2,3],[1,3,2],[2,1,3],[3,2,1]]
                nums[first],nums[i]=nums[i],nums[first]

        def duplicat(nums,begin,end):
            i=begin
            while i<end:
                if nums[i]==nums[end]:
                    return False
                i+=1
            return True
    
        lens=len(nums)
        output=[]
        backtrack()
        return output
```

```
class Solution:
    def permuteUnique(self, nums: List[int]) -> List[List[int]]:
        
        def backtrack(first=0):
            if first==lens:
                output.append(nums[:])
            for i in range(first,lens):
                
                if nums[i] not in nums[first:i]:
                    nums[first],nums[i]=nums[i],nums[first]
                    backtrack(first+1)
                    nums[first],nums[i]=nums[i],nums[first]
        
        lens=len(nums)
        output=[]
        nums.sort()
        backtrack()
        return output
```

参考：https://leetcode-cn.com/problems/permutations-ii/solution/hui-su-suan-fa-python-dai-ma-java-dai-ma-by-liwe-2/

```
class Solution:
    def permuteUnique(self, nums: List[int]) -> List[List[int]]:
        res=[]
        def backtrack(first,path,used):
            if first == len(nums):
                res.append(path[:])
                return
            for i in range(len(nums)):
                if not used[i]:
                    if i>0 and nums[i] == nums[i-1] and not used[i-1]:
                        continue
                    used[i]=True
                    path.append(nums[i])
                    backtrack(first+1,path,used)
                    used[i]=False
                    path.pop()
        nums.sort()
        used=[False] * len(nums)
        backtrack(0,[],used)
        return res
```

### 51. N皇后
    链接：https://leetcode-cn.com/problems/n-queens/

    n 皇后问题研究的是如何将 n 个皇后放置在 n×n 的棋盘上，并且使皇后彼此之间不能相互攻击。

    上图为 8 皇后问题的一种解法。

    给定一个整数 n，返回所有不同的 n 皇后问题的解决方案。

    每一种解法包含一个明确的 n 皇后问题的棋子放置方案，该方案中 'Q' 和 '.' 分别代表了皇后和空位。

    示例:

    输入: 4
    输出: [
     [".Q..",  // 解法 1
      "...Q",
      "Q...",
      "..Q."],

     ["..Q.",  // 解法 2
      "Q...",
      "...Q",
      ".Q.."]
    ]
    解释: 4 皇后问题存在两个不同的解法。

题解一|回溯法:

问题是十九世纪著名的数学家高斯1850年提出：在8X8格的国际象棋上摆放八个皇后（棋子），使其不能互相攻击，即任意两个皇后都不能处于同一行、同一列或同一斜线上。

1、判断每次输入的皇后是否在同一行、同一列或者同一斜线上
2、核心算法:https://leetcode-cn.com/problems/n-queens/solution/51-n-queenshui-su-fa-jing-dian-wen-ti-xiang-jie-by/

```
class Solution:
    def solveNQueens(self, n: int) -> List[List[str]]:
        
        def isValid(row,col):
            for i in range(row):
                for j in range(n): # 注意此处是n
                    if board[i][j]=='Q' and (j==col or i-j==row-col or i+j==row+col): # 判断列、对角线和斜对角线
                        return False
            return True
        def helper(row):
            if row==n: # 结束条件
                tmplist=[]
                for each in board:
                    tmpstr=''.join(each)
                    tmplist.append(tmpstr)
                res.append(tmplist)
                return

            for col in range(n):
                if isValid(row,col):
                    board[row][col]='Q'
                    helper(row+1)
                    board[row][col]='.'
            
        board=[['.']*n for i in range(n)]
        res=[]
        helper(0)
        return res
```

```
class Solution:
    def solveNQueens(self, n: int) -> List[List[str]]:
        board,ret=[['.']*n for _ in range(n)],[]
        self.dfs(board,n,0,ret)
        return ret
    def dfs(self,board,n,row,ret):
        if row==n:
            ret.append([''.join(i) for i in board])
            return
        for i in range(n):
            if not self.isVaild(row,i,n,board):
                continue
            board[row][i]='Q'
            self.dfs(board,n,row+1,ret)
            board[row][i]='.'

    def isVaild(self,row,col,n,board):
        for i in range(1, row + 1):
            # 判断同一列上是否有Q
            if board[row - i][col] == 'Q':
                return False
            # 判断逆对角线是否有Q
            if col - i >= 0 and board[row - i][col - i] == 'Q':
                return False
            # 判断正对角线是否有Q
            if col + i < n and board[row - i][col + i] == 'Q':
                return False
        return True

```

### 52. N皇后 II
    链接：https://leetcode-cn.com/problems/n-queens-ii/

    n 皇后问题研究的是如何将 n 个皇后放置在 n×n 的棋盘上，并且使皇后彼此之间不能相互攻击。

    上图为 8 皇后问题的一种解法。

    给定一个整数 n，返回 n 皇后不同的解决方案的数量。

    示例:

    输入: 4
    输出: 2
    解释: 4 皇后问题存在如下两个不同的解法。
    [
    [".Q..",  // 解法 1
    "...Q",
    "Q...",
    "..Q."],

    ["..Q.",  // 解法 2
    "Q...",
    "...Q",
    ".Q.."]
    ]
    
    提示：

    皇后，是国际象棋中的棋子，意味着国王的妻子。皇后只做一件事，那就是“吃子”。当她遇见可以吃的棋子时，就迅速冲上去吃掉棋子。当然，她横、竖、斜都可走一或 N-1 步，可进可退。（引用自 百度百科 - 皇后 ）

题解一|回溯：
```
class Solution:
    def __init__(self):
        self.count=0

    def totalNQueens(self, n: int) -> int:
        
        def isValid(row,col):
            for i in range(row):
                for j in range(n):
                    if board[i][j]=='Q' and (j==col or i-j==row-col or i+j==row+col):
                        return False
            return True
        
        def helper(row):
            if row==n:
                self.count+=1
                return
            for col in range(n):
                if isValid(row,col):
                    board[row][col]='Q'
                    helper(row+1)
                    board[row][col]='.'
        board=[['.']*n for i in range(n)]
        helper(0)
        return self.count
```

### 60. 第k个排列
    链接：https://leetcode-cn.com/problems/permutation-sequence/

    给出集合 [1,2,3,…,n]，其所有元素共有 n! 种排列。

    按大小顺序列出所有排列情况，并一一标记，当 n = 3 时, 所有排列如下：

    "123"
    "132"
    "213"
    "231"
    "312"
    "321"
    给定 n 和 k，返回第 k 个排列。

    说明：

    给定 n 的范围是 [1, 9]。
    给定 k 的范围是[1,  n!]。
    示例 1:

    输入: n = 3, k = 3
    输出: "213"
    示例 2:

    输入: n = 4, k = 9
    输出: "2314"

题解一|回溯|超时：
```
class Solution:
    def getPermutation(self, n: int, k: int) -> str:
        res=[]
        used=[False]*n # 使用used去重
        def backtrace(path,first):
            if len(path) == n:
                return res.append(path[:])

            for i in range(1,n+1):
                if used[i-1]: 
                    continue
                used[i-1]=True
                path.append(i)
                backtrace(path,i+1)
                used[i-1]=False
                path.pop()
        if not n or not k:
            return ''
        backtrace([],1)
        # print(res)
        tmp=[str(i) for i in res[k-1]]
        return ''.join(tmp)
```

优化|超时：

```
class Solution:
    def getPermutation(self, n: int, k: int) -> str:
        res=[]
        used=[False]*n # 使用used去重
        def backtrace(path):
            if len(res) == k:
                return
            
            if len(path) == n:
                return res.append(path[:])

            for i in range(1,n+1):
                if used[i-1]: 
                    continue
                used[i-1]=True
                path.append(i)
                backtrace(path)
                used[i-1]=False
                path.pop()
        if not n or not k:
            return ''
        backtrace([])
        tmp=[str(i) for i in res[-1]]
        return ''.join(tmp)
```

回溯|剪枝：
```
class Solution:
    def getPermutation(self, n: int, k: int) -> str:
        res=[]
        def backtrack(k,first):
            if first == n:
                return
            cnt=factorial[n-1-first]
            for i in range(1,n+1):
                if used[i]:
                    continue
                if cnt < k:
                    k-=cnt
                    continue
                res.append(i)
                used[i]=True
                backtrack(k,first+1)

        used=[False] * (n+1) # 使用used去重
        factorial=[ 1 for _ in range(n+1)]
        for i in range(2,n+1):
            factorial[i]=factorial[i-1]*i
        backtrack(k,0)
        return ''.join([str(num) for num in res])

```


### 77.组合
    链接：https://leetcode-cn.com/problems/combinations/

    给定两个整数 n 和 k，返回 1 ... n 中所有可能的 k 个数的组合。

    示例:

    输入: n = 4, k = 2
    输出:
    [
      [2,4],
      [3,4],
      [2,3],
      [1,2],
      [1,3],
      [1,4],
    ]

题解一|回溯：
```
class Solution:
    def combine(self, n: int, k: int) -> List[List[int]]:
        if k == 0 or n==0:
            return []
    
        res=[]
        path=[]
        return self.helper(n,k,res,path,1)
    def helper(self,n,k,res,path,first=1):
        if len(path)==k:
            res.append(path[:])
        for i in range(first,n+1):
            path.append(i)

            self.helper(n,k,res,path,i+1) 
            # self.helper(n,k,res,path,first+1) 
            # [[1,2],[1,3],[1,4],[2,2],[2,3],[2,4],[3,2],[3,3],[3,4],[4,2],[4,3],[4,4]]
            path.pop()
        return res
```

```
class Solution:
    def combine(self, n: int, k: int) -> List[List[int]]:
        res=[]
        def backtrace(path,first):
            if len(path) == k:
                return res.append(path[:])
            for i in range(first,n+1):
                path.append(i)
                backtrace(path,i+1)
                # backtrace(path,first+1)
                path.pop()
        if not n or not k:
            return []
        backtrace([],1)
        return res
```

```
class Solution:
    def combine(self, n: int, k: int) -> List[List[int]]:
        res=[]
        def backtrace(path,first):
            if len(path) == k:
                return res.append(path[:])
            for i in range(first,n+1):
                path.append(i)
                backtrace(path,i+1) # 输出：[[1,2],[1,3],[1,4],[2,3],[2,4],[3,4]]
                # backtrace(path,i) # 输出：[[1,1],[1,2],[1,3],[1,4],[2,2],[2,3],[2,4],[3,3],[3,4],[4,4]]
                # backtrace(path,first+1) # 输出：[[1,2],[1,3],[1,4],[2,2],[2,3],[2,4],[3,2],[3,3],[3,4],[4,2],[4,3],[4,4]]
                # backtrace(path,first) # 输出：[[1,1],[1,2],[1,3],[1,4],[2,1],[2,2],[2,3],[2,4],[3,1],[3,2],[3,3],[3,4],[4,1],[4,2],[4,3],[4,4]]
                path.pop()
        if not n or not k:
            return []
        backtrace([],1)
        return res
```

### 78.子集
    链接：https://leetcode-cn.com/problems/subsets/submissions/

    给定一组不含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。

    说明：解集不能包含重复的子集。

    示例:

    输入: nums = [1,2,3]
    输出:
    [
      [3],
      [1],
      [2],
      [1,2,3],
      [1,3],
      [2,3],
      [1,2],
      []
    ]
题解一|库函数:
```
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        import itertools
        res=[]
        for i in range(len(nums)+1):
            for j in itertools.combinations(nums,i):
                res.append(j)
        return res
```
题解二|迭代:
```
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        res=[[]]
        for i in nums:
            res+=[[i]+j for j in res]
            # print(res)
        return res

```
题解三|回溯:
https://leetcode-cn.com/problems/subsets/solution/hui-su-python-dai-ma-by-liweiwei1419/

```
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        length=len(nums)
        if length==0:
            return []
        
        res=[]
        path=[]
        return self.helper(nums,res,path,0)
    
    def helper(self,nums,res,path,first=1):
        res.append(path[:])
        for i in range(first,len(nums)):
            path.append(nums[i])
            self.helper(nums,res,path,i+1)
            path.pop()
        return res
```

```
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        res=[]
        def backtrace(path,first):
            res.append(path[:])
            for i in range(first,len(nums)):
                path.append(nums[i])
                backtrace(path,i+1)
                path.pop()
        backtrace([],0)
        return res
```

### 79. 单词搜索
    链接：https://leetcode-cn.com/problems/word-search/

    给定一个二维网格和一个单词，找出该单词是否存在于网格中。

    单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

    示例:

    board =
    [
      ['A','B','C','E'],
      ['S','F','C','S'],
      ['A','D','E','E']
    ]

    给定 word = "ABCCED", 返回 true
    给定 word = "SEE", 返回 true
    给定 word = "ABCB", 返回 false
     

    提示：

    board 和 word 中只包含大写和小写英文字母。
    1 <= board.length <= 200
    1 <= board[i].length <= 200
    1 <= word.length <= 10^3

题解一|DFS：
```
class Solution:
    def exist(self, board: List[List[str]], word: str) -> bool:
        def dfs(board,word,i,j,index):
            if i<0 or i>=m or j<0 or j>=n or board[i][j]!=word[index]:
                return False
            if index==len(word)-1:
                return True
            tmp=board[i][j]
            board[i][j]='.'
            res=dfs(board,word,i+1,j,index+1) or dfs(board,word,i-1,j,index+1) or dfs(board,word,i,j+1,index+1) or dfs(board,word,i,j-1,index+1)
            board[i][j]=tmp
            return res

        m=len(board)
        n=len(board[0])
        for i in range(m):
            for j in range(n):
                if dfs(board,word,i,j,0):
                    return True
        return False
```

### 90. 子集 II
    链接：https://leetcode-cn.com/problems/subsets-ii/submissions/

    给定一个可能包含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。

    说明：解集不能包含重复的子集。

    示例:

    输入: [1,2,2]
    输出:
    [
      [2],
      [1],
      [1,2,2],
      [2,2],
      [1,2],
      []
    ]

题解一|回溯:
```
class Solution:
    def subsetsWithDup(self, nums: List[int]) -> List[List[int]]:
        length=len(nums)
        if length==0:
            return []
        
        nums.sort() # 一定需要先排序，否则结果中有重复的。
        res=[]
        path=[]
        return self.helper(nums,res,path,0)
    
    def helper(self,nums,res,path,first=1):
        res.append(path[:])
        for i in range(first,len(nums)):
            if i>first and nums[i]==nums[i-1]:
                continue
            path.append(nums[i])
            self.helper(nums,res,path,i+1)
            path.pop()
        return res
```

https://leetcode-cn.com/problems/subsets-ii/solution/hui-su-de-duo-chong-xie-fa-duo-ti-yu-jing-by-zhang/

```
class Solution:
    def subsetsWithDup(self, nums: List[int]) -> List[List[int]]:
        res=[]
        nums.sort()
        def backtrace(path,first):
            res.append(path[:]) # # 没有return，加上return后不会进入回溯。
            for i in range(first,len(nums)):
                if i>first and nums[i]==nums[i-1]: # 滤重
                    continue
                path.append(nums[i])
                backtrace(path,i+1)
                path.pop()
        backtrace([],0)
        return res
```
### 93.复原IP地址
    链接：https://leetcode-cn.com/problems/restore-ip-addresses/

    给定一个只包含数字的字符串，复原它并返回所有可能的 IP 地址格式。

    示例:

    输入: "25525511135"
    输出: ["255.255.11.135", "255.255.111.35"]


注意：
    IP的格式,每位是在0~255之间,
    不能出现以0开头的两位以上数字,比如012,08...

题解一|暴力:
```
class Solution:
    def restoreIpAddresses(self, s: str) -> List[str]:
        n=len(s)
        res=[]
        
        def helper(tmp):
            if tmp=='' or (tmp[0]=='0' and len(tmp)>1) or int(tmp)>255:
            # if not tmp or (tmp[0]=='0' and len(tmp)>1) or int(tmp)>255:
                return False
            return True
        
        for i in range(3):
            for j in range(i+1,i+4):
                for k in range(j+1,j+4):
                    if i<n and j<n and k<n:
                        tmp1=s[:i+1]
                        tmp2=s[i+1:j+1]
                        tmp3=s[j+1:k+1]
                        tmp4=s[k+1:]

                        if all(map(helper,[tmp1,tmp2,tmp3,tmp4])):
                            res.append(tmp1+'.'+tmp2+'.'+tmp3+'.'+tmp4)
        return res
```
题解二|回溯:

参考：https://leetcode-cn.com/problems/restore-ip-addresses/solution/shou-hua-tu-jie-huan-yuan-dfs-hui-su-de-xi-jie-by-/

思路:

回溯的要点1：

    做第一步时我们有几种 选择 以 "25525511135" 为例：

        选 "2" 作为第一个片段
        选 "25" 作为第一个片段
        选 "255" 作为第一个片段

    有三种选择，做了选择后，又面临三种选择：再切出一种长度的片段作为第二个片段。
    这会像树一样向下分支，我们用 DFS 去遍历所有选择，并且是回溯，为什么是回溯？
    因为某一步的选择可能来到一个错误的状态，得不到正确的结果，不要往下做了，要撤销最后一个选择，回到选择前的状态，去试另一个选择。
    大概像是：选择、探索、撤销选择的过程。好，我们现在了解了回溯的要点1——选择。


回溯的要点2——约束:

    约束限制了我们的搜索分支，这道题的约束条件是：

        一个片段的长度是 1~3
        片段的值范围是 0~255
        不能是 "0x"、"0xx" 形式（这个是测试用例告诉我们的）
    
    我们要用这些约束在DFS中充分地剪枝，规避掉一些选择，避免去搜索一些不会产生正确答案的分支。

回溯的要点3——目标:

    我们的目标决定了我们 DFS 什么时候捕捉答案，什么时候砍掉死支，不继续走了返回出来。
    我们目标是生成 4 个有效片段，并且要用光 IP 字符串的字符。
    当遍历的节点满足该条件时，说明已经生成了一个有效组合，推入结果数组。然后回溯，继续探索。
    生成了4个有效片段，但没用光所有字符，不是有效的组合，不继续往下递归，直接返回，提前回溯。

定义dfs函数:

    dfs函数传什么，用什么代表不同节点的状态？
    选择切出一个长度的片段后，剩余的子串继续递归，可以传子串，也可以传指向起始位置的指针，加上当前已经生成的片段数组，作为状态，传入 dfs 函数。
    dfs 函数做的是「按一定规则，切割从指针 start 开始的子串」。


```
class Solution:
    def restoreIpAddresses(self, s: str) -> List[str]:
        res=[]
        def helper(path,first):
            if len(path)==4 and first==len(s):
                res.append('.'.join(path))
            if len(path)==4 and first < len(s):
                return
            for i in range(1,4):
                if first+i-1 >= len(s):
                    return
                if i >= 2 and s[first]=='0':
                    return
                tmp=s[first:first+i]                
                if i==3 and int(tmp)>255:
                    return
                path.append(tmp)
                helper(path,first+i)
                path.pop()
        helper([],0)
        return res
```

```
class Solution:
    def restoreIpAddresses(self, s: str) -> List[str]:
        res=[]
        def helper(path,first):
            if len(path)==4 and first==len(s):
                res.append('.'.join(path))
            if len(path)==4 and first < len(s):
                return
            for i in range(1,4):
                if first+i-1 >= len(s):
                    return
                if i >= 2 and s[first]=='0':
                    return
                tmp=s[first:first+i]                
                if i==3 and int(tmp)>255:
                    return
                helper(path+[tmp],first+i)
        helper([],0)
        return res
```

```
class Solution:
    def restoreIpAddresses(self, s: str) -> List[str]:
        def __segment(s,left,right):
            size=right-left+1

            if size > 1 and s[left]='0':
                return -1
            res=0
            for i in range(left,right+1):
                res=res*10 + ord(s[i])-ord('0')

            if res > 255:
                return -1
            return res

        def backtrace(split,first,path):
            if first == len(s):
                if split == 4:
                    res.append(''.join(path))
                return
            if len(s) - first
```

### 216. 组合总和 III
    链接：https://leetcode-cn.com/problems/combination-sum-iii/

    找出所有相加之和为 n 的 k 个数的组合。组合中只允许含有 1 - 9 的正整数，并且每种组合中不存在重复的数字。

    说明：

    所有数字都是正整数。
    解集不能包含重复的组合。 
    示例 1:

    输入: k = 3, n = 7
    输出: [[1,2,4]]
    示例 2:

    输入: k = 3, n = 9
    输出: [[1,2,6], [1,3,5], [2,3,4]]

题解一|回溯：
```
class Solution:
    def combinationSum3(self, k: int, n: int) -> List[List[int]]:
        res=[]
        def backtrace(path,first,n):
            if len(path)==k and n==0:
                return res.append(path[:])
            for i in range(first,10):
                tmp=n-i
                if tmp < 0:
                    continue
                path.append(i)
                backtrace(path,i+1,tmp)
                path.pop()
        if not k or not n:
            return []
        backtrace([],1,n)
        return res
```

### 357. 计算各个位数不同的数字个数
    链接：https://leetcode-cn.com/problems/count-numbers-with-unique-digits/

    给定一个非负整数 n，计算各位数字都不同的数字 x 的个数，其中 0 ≤ x < 10n 。

    示例:

    输入: 2
    输出: 91 
    解释: 答案应为除去 11,22,33,44,55,66,77,88,99 外，在 [0,100) 区间内的所有数字。

题解一|动态规划：
```
class Solution:
    def countNumbersWithUniqueDigits(self, n: int) -> int:
        if n==0:
            return 1
        dp1=[0]*(n+1)
        dp2=[0]*(n+1)
        dp1[1]=10
        dp2[1]=9
        for i in range(2,min(11,n+1)):
            dp2[i]=dp2[i-1]*(10-(i-1))
            dp1[i]=dp2[i]+dp1[i-1]
        if n>=11:
            return dp[10]
        return dp1[n]
```

题解二|数学：

这是一道数学题，很容易发现规律：

如果 n = 1，ans = 10；
如果 n = 2，考虑两位数都不相同，有 9 * 9 = 81 种情况（第一个数字不能以 0 开头，第二个数字可以有 0），再加上 n = 1 时的情况即可得到 ans = 91；
如果 n = 3，考虑三位数都不相同，有 9 * 9 * 8 = 648 种情况（第一个数字不能以 0 开头），再加上 n = 2 时的情况即可得到 ans = 739；
以此类推即可。

因此，我们从 i = 1 开始，每次累加结果，一直计算到 i = n 即可得到答案。注意：当 n > 10 时，与 n = 10 的结果相同。

```
class Solution:
    def countNumbersWithUniqueDigits(self, n: int) -> int:
        def factorial(cnt):  # 从9阶乘cnt次
            res = 1
            factor = 9
            for i in range(cnt):
                res *= factor
                factor -= 1
            return res
        
        pre = i = 1
        while i <= n and i <= 10:  # i要<=10
            pre += 9 * factorial(i-1)  # i位不同的数字与前面结果累加
            i += 1
        return pre
```

??题解三|回溯：
```
class Solution:
    def countNumbersWithUniqueDigits(self, n: int) -> int:
        used=[False]*10
        def backtrace(first,used):
            count=0
            if first != n:
                for i in range(10):
                    if i==0 and n>1 and first==1:
                        continue
                    if used[i]:
                        continue
                    used[i]=True
                    count+=backtrace(first+1,used)+1
                    used[i]=False
            return count
        if not n:
            return 1
        return backtrace(0,used)
```

### 401. 二进制手表
    链接：https://leetcode-cn.com/problems/binary-watch/

    二进制手表顶部有 4 个 LED 代表 小时（0-11），底部的 6 个 LED 代表 分钟（0-59）。

    每个 LED 代表一个 0 或 1，最低位在右侧。


    例如，上面的二进制手表读取 “3:25”。

    给定一个非负整数 n 代表当前 LED 亮着的数量，返回所有可能的时间。

    示例：

    输入: n = 1
    返回: ["1:00", "2:00", "4:00", "8:00", "0:01", "0:02", "0:04", "0:08", "0:16", "0:32"]
     

    提示：

    输出的顺序没有要求。
    小时不会以零开头，比如 “01:00” 是不允许的，应为 “1:00”。
    分钟必须由两位数组成，可能会以零开头，比如 “10:2” 是无效的，应为 “10:02”。
    超过表示范围（小时 0-11，分钟 0-59）的数据将会被舍弃，也就是说不会出现 "13:00", "0:61" 等时间。

题解一|暴力：
```
class Solution:
    def readBinaryWatch(self, num: int) -> List[str]:
        res=[]
        for i in range(12):
            for j in range(60):
                if (bin(i)+bin(j)).count('1') == num:
                    res.append('%d:%02d'%(i,j))
        return res     
```

扩展：

    %d就是普通的输出了整形数
    %2d是将数字按宽度为2，采用右对齐方式输出，若数据位数不到2位，则左边补空格。
    %02d，和%2d差不多，只不过左边补0

    %-2d将数字按宽度为2，采用左对齐方式输出，若数据位数不到2位，则右边补空格
    %.2d 输出整形时最少输出2位，如不够前面以0占位。如输出2时变成02，200时只输出200；输出浮点型时（%.2f）小数点后强制2位输出

比如：

    num = 1
    print("%d" % (num))结果为：   （1）
    print("%2d" % (num))结果为：  （ 1）
    print("%02d" % (num))结果为： （01）
    print("%-2d" % (num))结果为： （1 ）
    print("%.2d" % (num))结果为： （01）
    print("%.2d" % (200))结果为： （200）

题解二|回溯
```
class Solution:
    def readBinaryWatch(self, num: int) -> List[str]:
        
        def traceback(num,index,status):
            if num==0:
                hour=status[0]+2*status[1]+4*status[2]+8*status[3]
                mintue=status[4]+2*status[5]+4*status[6]+8*status[7]+16*status[8]+32*status[9]
                if hour<12 and mintue <60:
                    res.append('%d:%02d'%(hour,mintue))
                return
            for i in range(index,10):
                status[i]=1
                traceback(num-1,i+1,status)
                status[i]=0
        res=[]
        traceback(num,0,[0]*10)
        return res
```

```
    def readBinaryWatch(self, num: int) -> List[str]:
        """
        思路： 递归回溯法
        """
        if num < 0:
            return []
        # 一共只有10个灯
        res, hour, minute = [], [1, 2, 4, 8], [1, 2, 4, 8, 16, 32]
        def traceback(num, index, status):
            """
            num : 还剩下的可点亮的灯的数量
            index: 是当前选择点亮哪一个灯
            status: 记录是哪些位置的灯被点亮了
            
            """
            if num == 0: # 没有可以点亮的灯了，记录当前结果，并返回
                h = sum([i*j for i,j in zip(hour, status[:4])])
                m = sum([i*j for i,j in zip(minute, status[4:])])
                if h < 12 and m < 60: 
                    res.append('%d:%02d' % (h, m))
                return
            for i in range(index, 10):
                status[i] = 1 # 做出下一步的选择
                traceback(num-1, i+1, status) # 在剩下的灯里面去点亮num-1个灯
                status[i] = 0 # 返回到上一步选择时的状态            
        
        traceback(num, 0, [0] * 10)
        return res
```

### 784. 字母大小写全排列

    链接：https://leetcode-cn.com/problems/letter-case-permutation/

    给定一个字符串S，通过将字符串S中的每个字母转变大小写，我们可以获得一个新的字符串。返回所有可能得到的字符串集合。

    示例:
    输入: S = "a1b2"
    输出: ["a1b2", "a1B2", "A1b2", "A1B2"]

    输入: S = "3z4"
    输出: ["3z4", "3Z4"]

    输入: S = "12345"
    输出: ["12345"]
    注意：

    S 的长度不超过12。
    S 仅由数字和字母组成。

题解一|递归：

```
class Solution:
    def letterCasePermutation(self, S: str) -> List[str]:
        res=[]

        def dfs(s,tmp):
            if not s:
                res.append(''.join(tmp))
                return
            if s[0].isalpha():
                dfs(s[1:],tmp+[s[0].upper()])
                dfs(s[1:],tmp+[s[0].lower()])
            else:
                dfs(s[1:],tmp+[s[0]])
        dfs(S,[])
        return res
```

题解二|迭代
```
class Solution:
    def letterCasePermutation(self, S: str) -> List[str]:
        res=['']
        for i,c in enumerate(S):
            if c.isdigit():
                for j,r in enumerate(res):
                    res[j]=r+c
            else:
                tmp=[]
                for r in res:
                    tmp.append(r+c.upper())
                    tmp.append(r+c.lower())
                res=tmp[:]
        return res
```

### 剑指 Offer 38. 字符串的排列
    链接：https://leetcode-cn.com/problems/zi-fu-chuan-de-pai-lie-lcof/

    输入一个字符串，打印出该字符串中字符的所有排列。

    你可以以任意顺序返回这个字符串数组，但里面不能有重复元素。

    示例:

    输入：s = "abc"
    输出：["abc","acb","bac","bca","cab","cba"]
     

    限制：

    1 <= s 的长度 <= 8

题解一|回溯：
```
class Solution:
    def permutation(self, s: str) -> List[str]:
        res=[]
        c=list(s)
        
        def backtrace(first):
            if len(s) == first:  # 不能使用len(s)==len(c)作为结束条件，因为c其实没变。
                return res.append(''.join(c))
            isDup=set()
            for i in range(first,len(c)):
                if c[i] in isDup: # 还有一种简单去重方法，直接把res定义为set()
                    continue
                isDup.add(c[i])
                c[first],c[i]=c[i],c[first]
                backtrace(first+1)
                c[first],c[i]=c[i],c[first]
        backtrace(0)
        return res
```

```
class Solution:
    def permutation(self, s: str) -> List[str]:
        res=[]
        list_s=list(s)
        list_s.sort()
        self.sort_s=''.join(list_s)
        used=[False]*len(s)        
        
        def backtrace(path,first):
            if len(self.sort_s) == len(path): 
                return res.append(''.join(path[:]))
            for i in range(len(self.sort_s)):
                if used[i]:
                    continue
                if i>0 and self.sort_s[i]==self.sort_s[i-1] and used[i-1] == False: # ？？？？
                    continue
                used[i]=True
                path.append(self.sort_s[i])
                backtrace(path,first+1)
                path.pop()
                used[i]=False
        backtrace([],0)
        return res
```

### 面试题 08.04. 幂集
    幂集。编写一种方法，返回某集合的所有子集。集合中不包含重复的元素。

    说明：解集不能包含重复的子集。

    示例:

     输入： nums = [1,2,3]
     输出：
    [
      [3],
      [1],
      [2],
      [1,2,3],
      [1,3],
      [2,3],
      [1,2],
      []
    ]

题解一|回溯：
```
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        res=[]
        def backtrace(path,first):
            res.append(path[:])
            for i in range(first,len(nums)):
                path.append(nums[i])
                backtrace(path,i+1)
                path.pop()
        backtrace([],0)
        return res
        
```

### 面试题 08.07. 无重复字符串的排列组合
    无重复字符串的排列组合。编写一种方法，计算某字符串的所有排列组合，字符串每个字符均不相同。

    示例1:

     输入：S = "qwe"
     输出：["qwe", "qew", "wqe", "weq", "ewq", "eqw"]
    示例2:

     输入：S = "ab"
     输出：["ab", "ba"]
    提示:

    字符都是英文字母。
    字符串长度在[1, 9]之间。

题解一|回溯:
```
class Solution:
    def permutation(self, S: str) -> List[str]:
        res=[]
        c=list(S)
        
        def backtrace(first):
            if len(S) == first:  # 不能使用len(s)==len(c)作为结束条件，因为c其实没变。
                return res.append(''.join(c))
            for i in range(first,len(c)):
                c[first],c[i]=c[i],c[first]
                backtrace(first+1)
                c[first],c[i]=c[i],c[first]
        backtrace(0)
        return res
```

### 面试题 08.09. 括号

    括号。设计一种算法，打印n对括号的所有合法的（例如，开闭一一对应）组合。

    说明：解集不能包含重复的子集。

    例如，给出 n = 3，生成结果为：

    [
      "((()))",
      "(()())",
      "(())()",
      "()(())",
      "()()()"
    ]

```
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        res=[]
        def backtrace(path,first):
            if len(path) == 2*n:
                if first==0:
                    res.append(''.join(path))
                return
            if first < n:
                path.append('(')
                backtrace(path,first+1)
                path.pop()
            if first > 0:
                path.append(')')
                backtrace(path,first-1)
                path.pop()
            print(path)
        backtrace([],0)
        return res
```
