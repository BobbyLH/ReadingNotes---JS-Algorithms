# 链表(Linked List)
**链表** 是一种线性数据的集合，但线性的顺序并不意味着会以同样的顺序存在于物理内存中。本质上链表内的每个元素节点只不过是保存了指向下一个元素节点的指针，而这些元素节点通过指针指向，在一起组成了有顺序的链表。

一个最基本的节点元素应该由它包含的数据和指向下一个元素的引用(指针)而组成，这样的数据结构对于 插入 和 删除 操作比较高效；而它的不足在于所花费的时间都是线性的，这也导致难以管道化(pipeline)，而且对于快速访问，比如随机访问，是受到限制的。和链表相比，数组在 高速缓存定位(cache locality) 方面有更好的表现。

## 代码剖析

### 子节点
```js
class LinkedListNode {
  constructor (value, next = null) {
    this.value = value; // 节点所包含的数据
    this.next = next; // 节点对下一个节点的引用，初始化时可以为空
  }

  // 获取节点数据的方法
  // 可以接受一个回调函数，并将节点包含的数据作为参数，否则直接返回隐式转换成字符串类型的数据
  toString (callback) {
    return callback ? callback(this.value) : `${this.value}`;
  }
}
```

### 进行比较的Comparator
```js
class Comparator {
  constructor (compareFunction) {
    // this.compare 为比较方法，可以初始化时传入，否则使用默认的比较方法
    this.compare = compareFunction || Comparator.defaultCompareFunction;
  }

  // Comparator 默认进行比较的静态方法
  static defaultCompareFunction (a, b) {
    if (a === b) return 0;

    return a < b ? -1 : 1;
  } 
}
```

### 链表
```js
class LinkedList {
  constructor () {

  }
}
```

#### 时间复杂度
| Access    | Search    | Insertion | Deletion  |
| :-------: | :-------: | :-------: | :-------: |
| O(n)      | O(n)      | O(1)      | O(n)      |