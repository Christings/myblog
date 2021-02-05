---
title: LeetCode-17-设计
date: 2020-10-29 21:24:44
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---

### 155. 最小栈
    链接：https://leetcode-cn.com/problems/min-stack/solution/

    设计一个支持 push ，pop ，top 操作，并能在常数时间内检索到最小元素的栈。

    push(x) —— 将元素 x 推入栈中。
    pop() —— 删除栈顶的元素。
    top() —— 获取栈顶元素。
    getMin() —— 检索栈中的最小元素。
     
    示例:

    输入：
    ["MinStack","push","push","push","getMin","pop","top","getMin"]
    [[],[-2],[0],[-3],[],[],[],[]]

    输出：
    [null,null,null,null,-3,null,0,-2]

    解释：
    MinStack minStack = new MinStack();
    minStack.push(-2);
    minStack.push(0);
    minStack.push(-3);
    minStack.getMin();   --> 返回 -3.
    minStack.pop();
    minStack.top();      --> 返回 0.
    minStack.getMin();   --> 返回 -2.
     
    提示：

    pop、top 和 getMin 操作总是在 非空栈 上调用。

题解一|双栈：

```
class MinStack:

    def __init__(self):
        """
        initialize your data structure here.
        """
        self.stack=[]
        self.minstack=[]

    def push(self, x: int) -> None:
        self.stack.append(x)
        if not self.minstack or self.minstack[-1]>=x:
            self.minstack.append(x)

    def pop(self) -> None:
        if self.stack:
            if self.stack[-1] == self.minstack[-1]:
                self.minstack.pop()
            return self.stack.pop()

    def top(self) -> int:
        if self.stack:
            return self.stack[-1]
        else:
            return 

    def getMin(self) -> int:
        if self.minstack:
            return self.minstack[-1]
        else:
            return

# Your MinStack object will be instantiated and called as such:
# obj = MinStack()
# obj.push(x)
# obj.pop()
# param_3 = obj.top()
# param_4 = obj.getMin()
```

### 146. LRU 缓存机制
    链接：https://leetcode-cn.com/problems/lru-cache/

    运用你所掌握的数据结构，设计和实现一个  LRU (最近最少使用) 缓存机制 。
    实现 LRUCache 类：

    LRUCache(int capacity) 以正整数作为容量 capacity 初始化 LRU 缓存
    int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
    void put(int key, int value) 如果关键字已经存在，则变更其数据值；如果关键字不存在，则插入该组「关键字-值」。当缓存容量达到上限时，它应该在写入新数据之前删除最久未使用的数据值，从而为新的数据值留出空间。
    

    进阶：你是否可以在 O(1) 时间复杂度内完成这两种操作？


    示例：

    输入
    ["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
    [[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
    输出
    [null, null, null, 1, null, -1, null, -1, 3, 4]

    解释
    LRUCache lRUCache = new LRUCache(2);
    lRUCache.put(1, 1); // 缓存是 {1=1}
    lRUCache.put(2, 2); // 缓存是 {1=1, 2=2}
    lRUCache.get(1);    // 返回 1
    lRUCache.put(3, 3); // 该操作会使得关键字 2 作废，缓存是 {1=1, 3=3}
    lRUCache.get(2);    // 返回 -1 (未找到)
    lRUCache.put(4, 4); // 该操作会使得关键字 1 作废，缓存是 {4=4, 3=3}
    lRUCache.get(1);    // 返回 -1 (未找到)
    lRUCache.get(3);    // 返回 3
    lRUCache.get(4);    // 返回 4
    

    提示：

    1 <= capacity <= 3000
    0 <= key <= 3000
    0 <= value <= 104
    最多调用 3 * 104 次 get 和 put

题解一|OrderedDict：
```
import collections

class LRUCache(collections.OrderedDict):

    def __init__(self, capacity: int):
        super().__init__()
        self.capacity=capacity

    def get(self, key: int) -> int:
        if key not in self:
            return -1
        self.move_to_end(key)
        return self[key]


    def put(self, key: int, value: int) -> None:
        if key in self:
            self.move_to_end(key) # 表达把key移动到最后，即更新key
        self[key]=value
        if len(self)>self.capacity:
            self.popitem(last=False) # last=False表示先进先出，last=True表示后进先出


# Your LRUCache object will be instantiated and called as such:
# obj = LRUCache(capacity)
# param_1 = obj.get(key)
# obj.put(key,value)
```

题解二|hash|双向链表：

时间复杂度：对于 put 和 get 都是 O(1)O(1)。

空间复杂度：O(capacity)，因为哈希表和双向链表最多存储 capacity+1 个元素。

```
class DLinkedNode:
    def __init__(self,key=0,value=0):
        self.key=key
        self.value=value
        self.prev=None
        self.next=None

class LRUCache:

    def __init__(self, capacity: int):
        self.cache=dict()
        self.head=DLinkedNode()
        self.tail=DLinkedNode()
        self.head.next=self.tail
        self.tail.prev=self.head
        self.capacity=capacity
        self.size=0

    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1
        node=self.cache[key]
        self.moveToHead(node)
        return node.value

    def put(self, key: int, value: int) -> None:
        if key not in self.cache:
            node=DLinkedNode(key,value)
            self.cache[key]=node
            self.addToHead(node)
            self.size+=1
            if self.size>self.capacity:
                removed=self.removeTail()
                self.cache.pop(removed.key)
                self.size-=1
        else:
            node=self.cache[key]
            node.value=value
            self.moveToHead(node)

    def addToHead(self,node):
        node.prev=self.head
        node.next=self.head.next
        self.head.next.prev=node
        self.head.next=node
    
    def removeNode(self,node):
        node.prev.next=node.next
        node.next.prev=node.prev
    
    def moveToHead(self,node):
        self.removeNode(node)
        self.addToHead(node)

    def removeTail(self):
        node=self.tail.prev
        self.removeNode(node)
        return node

# Your LRUCache object will be instantiated and called as such:
# obj = LRUCache(capacity)
# param_1 = obj.get(key)
# obj.put(key,value)
```

### 380. 常数时间插入、删除和获取随机元素
    链接：https://leetcode-cn.com/problems/insert-delete-getrandom-o1/

    设计一个支持在平均 时间复杂度 O(1) 下，执行以下操作的数据结构。

    insert(val)：当元素 val 不存在时，向集合中插入该项。
    remove(val)：元素 val 存在时，从集合中移除该项。
    getRandom：随机返回现有集合中的一项。每个元素应该有相同的概率被返回。
    示例 :

    // 初始化一个空的集合。
    RandomizedSet randomSet = new RandomizedSet();

    // 向集合中插入 1 。返回 true 表示 1 被成功地插入。
    randomSet.insert(1);

    // 返回 false ，表示集合中不存在 2 。
    randomSet.remove(2);

    // 向集合中插入 2 。返回 true 。集合现在包含 [1,2] 。
    randomSet.insert(2);

    // getRandom 应随机返回 1 或 2 。
    randomSet.getRandom();

    // 从集合中移除 1 ，返回 true 。集合现在包含 [2] 。
    randomSet.remove(1);

    // 2 已在集合中，所以返回 false 。
    randomSet.insert(2);

    // 由于 2 是集合中唯一的数字，getRandom 总是返回 2 。
    randomSet.getRandom();

题解一|hash|动态数组：

1、insert：

    平均插入时间为O(1) 的选择：
        哈希表：Java 中为 HashMap，Python 中为 dictionary。
        动态数组：Java 中为 ArrayList，Python 中为 list。

2、getRandom：

    哈希表提供常数时间的插入和删除，但是实现 getRandom 时会出现问题。
    getRandom 的思想是选择一个随机索引，然后使用该索引返回一个元素。而哈希表中没有索引，因此要获得真正的随机值，则要将哈希表中的键转换为列表，这需要线性时间。解决的方法是用一个列表存储值，并在该列表中实现常数时间的 getRandom。

3、remove：

    列表有索引可以实现常数时间的 insert 和 getRandom，则接下来的问题是如何实现常数时间的 remove。

    删除任意索引元素需要线性时间，这里的解决方案是总是删除最后一个元素。
        将要删除元素和最后一个元素交换。
        将最后一个元素删除。

综上：

    动态数组存储元素值。
    哈希表存储存储值到索引的映射。
    
```
import random
class RandomizedSet(object):

    def __init__(self):
        """
        Initialize your data structure here.
        """
        self.list=[]
        self.dict={}


    def insert(self, val):
        """
        Inserts a value to the set. Returns true if the set did not already contain the specified element.
        :type val: int
        :rtype: bool
        """
        if val in self.dict:
            return False
        self.dict[val]=len(self.list)
        self.list.append(val)
        return True

    def remove(self, val):
        """
        Removes a value from the set. Returns true if the set contained the specified element.
        :type val: int
        :rtype: bool
        """
        if val in self.dict:
            last,index=self.list[-1],self.dict[val]
            self.list[index],self.dict[last]=last,index
            self.list.pop()
            del self.dict[val]
            return True
        return False


    def getRandom(self):
        """
        Get a random element from the set.
        :rtype: int
        """
        return random.choice(self.list)

# Your RandomizedSet object will be instantiated and called as such:
# obj = RandomizedSet()
# param_1 = obj.insert(val)
# param_2 = obj.remove(val)
# param_3 = obj.getRandom()
```

### 384. 打乱数组
    链接：https://leetcode-cn.com/problems/shuffle-an-array/

    打乱一个没有重复元素的数组。

    示例:

    // 以数字集合 1, 2 和 3 初始化数组。
    int[] nums = {1,2,3};
    Solution solution = new Solution(nums);

    // 打乱数组 [1,2,3] 并返回结果。任何 [1,2,3]的排列返回的概率应该相同。
    solution.shuffle();

    // 重设数组到它的初始状态[1,2,3]。
    solution.reset();

    // 随机返回数组[1,2,3]打乱后的结果。
    solution.shuffle();

拷贝知识：

    直接赋值：其实就是对象的引用（别名）。

    浅拷贝(copy)：拷贝父对象，不会拷贝对象的内部的子对象。

    深拷贝(deepcopy)： copy 模块的 deepcopy 方法，完全拷贝了父对象及其子对象。

题解一|暴力：

时间复杂度： O(n^2)，乘方时间复杂度来自于 list.remove（list.pop）。每次操作都是线性时间的，总共发生 n 次。

空间复杂度： O(n)，因为需要实现 重置 方法，需要额外的空间把原始数组另存一份，在重置的时候用来恢复原始状态。

```
class Solution:
    from copy import copy
    from random import random
    def __init__(self, nums: List[int]):
        self.ori=copy.deepcopy(nums)
        self.nums=nums

    def reset(self) -> List[int]:
        """
        Resets the array to its original configuration and return it.
        """
        return self.ori

    def shuffle(self) -> List[int]:
        """
        Returns a random shuffling of the array.
        """
        self.aux=copy.deepcopy(self.nums)
        for i in range(len(self.nums)):
            index=random.randrange(len(self.aux))
            self.nums[i]=self.aux.pop(index)
        return self.nums

# Your Solution object will be instantiated and called as such:
# obj = Solution(nums)
# param_1 = obj.reset()
# param_2 = obj.shuffle()
```

题解二| Fisher-Yates 洗牌算法：

时间复杂度 ： O(n)，Fisher-Yates 洗牌算法时间复杂度是线性的，因为算法中生成随机序列，交换两个元素这两种操作都是常数时间复杂度的。

空间复杂度： O(n)，因为要实现 重置 功能，原始数组必须得保存一份，因此空间复杂度并没有优化。

```
class Solution:
    from copy import copy
    from random import random
    def __init__(self, nums: List[int]):
        self.ori=copy.deepcopy(nums)
        self.nums=nums

    def reset(self) -> List[int]:
        """
        Resets the array to its original configuration and return it.
        """
        return self.ori
        
    def shuffle(self) -> List[int]:
        """
        Returns a random shuffling of the array.
        """
        for i in range(len(self.nums)):
            index=random.randint(i,len(self.nums)-1)
            self.nums[i],self.nums[index]=self.nums[index],self.nums[i]
        return self.nums
        
# Your Solution object will be instantiated and called as such:
# obj = Solution(nums)
# param_1 = obj.reset()
# param_2 = obj.shuffle()
```

### 1678. 设计 Goal 解析器
    链接：https://leetcode-cn.com/problems/goal-parser-interpretation/

    请你设计一个可以解释字符串 command 的 Goal 解析器 。command 由 "G"、"()" 和/或 "(al)" 按某种顺序组成。Goal 解析器会将 "G" 解释为字符串 "G"、"()" 解释为字符串 "o" ，"(al)" 解释为字符串 "al" 。然后，按原顺序将经解释得到的字符串连接成一个字符串。

    给你字符串 command ，返回 Goal 解析器 对 command 的解释结果。

    

    示例 1：

    输入：command = "G()(al)"
    输出："Goal"
    解释：Goal 解析器解释命令的步骤如下所示：
    G -> G
    () -> o
    (al) -> al
    最后连接得到的结果是 "Goal"
    示例 2：

    输入：command = "G()()()()(al)"
    输出："Gooooal"
    示例 3：

    输入：command = "(al)G(al)()()G"
    输出："alGalooG"
    

    提示：

    1 <= command.length <= 100
    command 由 "G"、"()" 和/或 "(al)" 按某种顺序组成

题解一：
```
class Solution:
    def interpret(self, command: str) -> str:
        ans=''
        i=0
        while i<len(command):
            if command[i]=='G':
                ans+='G'
                i+=1
            elif command[i:i+2]=='()':
                ans+='o'
                i+=2
            elif command[i:i+4]=='(al)':
                ans+='al'
                i+=4
        return ans
```
