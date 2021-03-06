# 链表(Linked List)
**链表** 是一种线性数据的集合，但线性的顺序并不意味着会以同样的顺序存在于物理内存中。本质上链表内的每个元素节点只不过是保存了指向下一个元素节点的指针，而这些元素节点通过指针指向，在一起组成了有顺序的链表。

链表中，一个最基本的节点元素，应该由它包含的数据`this.value` 和 指向下一个元素的引用(指针)`this.next` 而组成。

像链表这样的数据结构，对于 **插入** 和 **删除** 操作比较高效，特别是在数据量很大的时候，只需要改变指针的指向，而不需要像数组一样改变所有元素的位置；而它的不足在于，查找每一个元素所花费的时间都是线性的，因为我们必须从头开始逐个遍历，而这也导致难以管道化(pipeline)，而且对于快速访问，比如随机访问，就不太给力了。和链表相比，数组在 高速缓存定位(cache locality) 方面有更好的表现。

## 代码剖析
<details open>
<summary>收起 / 展开</summary>

### LinkedListNode 类 - 节点
- 用 `this.value` 储存了节点的值

- 用 `this.next` 保存下一个节点的地址

- 实现了一个 `toString` 的方法，用于获取节点的值

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

### LinkedList 类 - 链表
- 用 `this.head` 保存链表的头部节点

- 用 `this.tail` 保存链表的尾部节点

- 实现了一个 `compare` 的方法，用于对比节点的值

  ```js
  class LinkedList {
    constructor (compareFunction) {
      this.head = null; // 链表的头
      this.tail = null; // 链表的尾

      this.compare = new Comparator(compareFunction);
    }
  }
  ```

#### prepend - 往头部追加一个节点
- 实例化一个 `LinkedListNode`，并将其 “插入” 到链表的头部

- 入参(Params)：
    - `value` <any> 待新增的节点的值

- 出参(Returns)：<LinkedList> 返回链表的整个实例

  ```js
  prepend (value) {
    // 新增节点的 next 为当前的头部节点 this.head
    const newNode = new LinkedListNode(value, this.head);

    // 将链表的头部指向新增的节点
    this.head = newNode;

    if (!this.tail) {
      // 如果链表没有尾部节点，那么将新增的节点同样视为是尾部节点
      this.tail = newNode;
    }

    // 返回整个链表的上下文
    return this;
  }
  ```

#### append - 往尾部追加一个节点
- 实例化一个 `LinkedListNode`，并将其 “插入” 到链表的尾部

- 入参(Params)：
    - `value` <any> 待新增的节点的值

- 出参(Returns)：<LinkedList> 返回链表的整个实例

  ```js
  append (value) {
    const newNode = new LinkedListNode(value);

    if (!this.head) {
      // 链表没有头部节点意味着这是个空的链表，因此将新增的节点同时赋值给头部和尾部的指针
      this.head = newNode;
      this.tail = newNode;

      return this; // 返回整个链表的上下文
    }

    // 将当前链表的尾部节点的next指向新增的节点
    this.tail.next = newNode;
    // 将链表的尾部指针指向新增的节点
    this.tail = newNode;

    return this; // 返回整个链表的上下文
  }
  ```

#### delete - 删除指定值的节点
- 使用 `this.compare.equal` 方法，根据传入的值匹配需要删除的所有节点

- 入参(Params)：
    - `value` <any> 待删除的节点的值

- 出参(Returns)：<LinkedListNode> 返回删除的节点

  ```js
  delete (value) {
    if (!this.head) {
      // 链表没有头部，意味着是一个空的链表，没有任何节点可供删除
      return null;
    }

    let deleteNode = null; // 准备删除的节点

    // 如果链表头部节点的值和要删除的值相等
    while(this.head && this.compare.equal(this.head.value, value)) {
      // 将当前头部节点赋值给准备删除的节点
      deleteNode = this.head;
      // 将头部节点移除，即将this.head指向当前头部节点的next下一个节点
      this.head = this.head.next;
      // 继续看当前的头部节点的值是否和要删除的值相等，相等的话继续循环体内部的操作
    }

    let currentNode = this.head; // 经过头部值过滤的头部节点(不必被删除)，将其视为开始遍历的起点

    if (currentNode !== null) {
      // 将整个链表遍历到尾部，因为尾部的 next 是 null
      while (currentNode.next) {
        if (this.compare.equal(currentNode.next.value, value)) {
          deletedNode = currentNode.next;
          currentNode.next = currentNode.next.next; // 将这个满足删除条件的节点从链表中移除
        } else {
          currentNode = currentNode.next;
        }
      }
    }

    // 尾部节点单独的排查
    if (this.compare.equal(this.tail.value, value)) {
      this.tail = currentNode;
    }

    return deleteNode; // 返回最后一个被删除的节点，可能是 null
  }
  ```

#### find - 查找指定值的节点
- 使用 自定义的回调函数 `callback` 或 `this.compare.equal` 方法，找出和参数 value 匹配的节点

- 入参(Params)：
    - `findParams` <{ value: any; callback?: (value: any) => boolean; }> 查找的参数

- 出参(Returns)：<LinkedListNode> 返回匹配的节点

  ```js
  find ({value = undefined, callback = undefined}) {
    if (!this.head) return null; // 没有 this.head 证明链表内没有任何节点

    let currentNode = this.head;
    while (currentNode) {
      // 指定了回调函数，则调用它来找出相应的节点
      if (callback && callback(currentNode.value)) {
        return currentNode;
      }

      // 如果指定了value，那么则根据value和当前遍历节点的value做对比
      if (value !== undefined && this.compare.equal(currentNode.value, value)) {
        return currentNode;
      }

      // 上述情况都不成立，则继续迭代，直至最后一个节点
      currentNode = currentNode.next;
    }

    // 整个链表遍历下来都没找到对应的节点，则返回null
    return null;
  }
  ```

#### deleteTail - 删除最后一个节点
- 删除完成后会重新绑定 `this.tail`

- 出参(Returns)：<LinkedListNode> 返回删除的节点

  ```js
  deleteTail () {
    const deleteTail = this.tail;

    if (this.head === deleteTail) {
      // 处理边界情况 —— 链表中只有一个元素
      this.head = null;
      this.tail = null;

      return deleteTail;
    }

    let currentNode = this.head;
    while (currentNode.next) {
      if (!currentNode.next.next) {
        // 该节点的 next 就是 this.tail
        // 即该节点是 this.tail 的上一个节点
        // 移除它们之间的关联
        currentNode.next = null;
      } else {
        currentNode = currentNode.next;
      }
    }

    // this.tail 指向它的上一个节点
    this.tail = currentNode;

    return deleteTail;
  }
  ```

#### deleteHead - 删除第一个节点
- 删除完成后会重新绑定 `this.head`

- 出参(Returns)：<LinkedListNode> 返回删除的节点

  ```js
  deleteHead () {
    if (!this.head) {
      // 没有 this.head 证明链表内没有任何节点
      return null
    }

    const deleteHead = this.head;

    if (this.head.next) {
      // this.head 指向它的下一个节点
      this.head = this.head.next;
    } else {
      // 边界情况 —— 只有一个元素
      this.head = null;
      this.tail = null;
    }

    return deleteHead;
  }
  ```

#### fromArray - 将一组值作为节点添加到链表后
- 遍历传入的值，并调用 `this.append` 将值作为节点添加到链表后

- 入参(Params)：
    - `values` <any[]> 一组需要被添加成节点的值

- 出参(Returns)：<LinkedList> 返回整个实例化的链表

  ```js
  fromArray (values) {
    values.forEach(value => this.append(value));

    return this;
  }
  ```

#### toArray - 将链表的所有节点组装成一个数组后返回
- 从链表头部开始，逐个遍历链表的所有节点，并组合成一个数组后返回

- 出参(Returns)：<LinkedListNode[]> 返回整个实例化的链表

  ```js
  toArray () {
    const nodes = [];

    let currentNode = this.head;
    while (currentNode) {
      nodes.push(currentNode);
      currentNode = currentNode.next;
    }

    return nodes;
  }
  ```

#### toString - 将链表中所有节点的值转换成字符串
- 调用 `this.toArray` 将值链表中的所有节点组装成一个数组

- 而后遍历整个数组的节点，并调用定义在节点中的 `toString` 方法，将节点转换成字符串

- 最终对整个数组调用 `toString` 方法，将整个数组隐式转换成字符串

- 入参(Params)：
    - `callback` <(value: any) => string> 自定义用于将节点转换成字符的回调函数

- 出参(Returns)：<string> 返回整个字符化后的链表

  ```js
  toString (callback) {
    return this.toArray().map(node => node.toString(callback)).toString();
  }
  ```

#### reverse - 将链表中节点的顺序完全颠倒
- 本质上来看，就是通过改变每个节点的 `this.next` 属性，实现了颠倒，当然要记得将链表的 `this.head` 和 `this.tail` 重新赋值

- 出参(Returns)：<LinkedList> 返回整个实例化的链表

  ```js
  reverse () {
    let currNode = this.head; // 先从 this.head 开始
    let prevNode = null;
    let nextNode = null;

    while (currNode) {
      // 将当前节点的 next 指向的节点保存下来
      nextNode = currNode.next;

      // 而后交换next和当前节点的位置
      currNode.next = prevNode;

      // 每次保存当前节点，直到最后一个节点，而后会将这个节点赋值给 this.head
      prevNode = currNode;
      // 遍历下一个节点
      currNode = nextNode;
    }

    // 最后一步是重置 this.head 和 this.tail
    this.tail = this.head;
    this.head = prevNode;

    return this;
  }
  ```
</details>

## 完整的代码
<details>
<summary>收起 / 展开</summary>

### 节点
```js
class LinkedListNode {
  constructor (value, next = null) {
    this.value = value;
    this.next = next;
  }

  toString (callback) {
    return callback ? callback(this.value) : `${this.value}`;
  }
}
```

### 链表
```js
import LinkedListNode from './LinkedListNode';
import Comparator from '../../utils/comparator';

class LinkedList {
  constructor (compareFunction) {
    this.head = null;
    this.tail = null;

    this.compare = new Comparator(compareFunction);
  }

  prepend (value) {
    const newNode = new LinkedListNode(value, this.head);
    this.head = newNode;

    if (!this.tail) {
      this.tail = newNode;
    }

    return this;
  }

  append (value) {
    const newNode = new LinkedListNode(value);

    if (!this.head) {
      this.head = newNode;
      this.tail = newNode;

      return this;
    }

    this.tail.next = newNode;
    this.tail = newNode;

    return this;
  }

  delete (value) {
    if (!this.head) {
      return null;
    }

    let deleteNode = null;

    while(this.head && this.compare.equal(this.head.value, value)) {
      deleteNode = this.head;
      this.head = this.head.next;
    }

    let currentNode = this.head;

    if (currentNode !== null) {
      while (currentNode.next) {
        if (this.compare.equal(currentNode.next.value, value)) {
          deletedNode = currentNode.next;
          currentNode.next = currentNode.next.next;
        } else {
          currentNode = currentNode.next;
        }
      }
    }

    if (this.compare.equal(this.tail.value, value)) {
      this.tail = currentNode;
    }

    return deleteNode;
  }

  find ({value = undefined, callback = undefined}) {
    if (!this.head) return null;

    while (currentNode) {
      if (callback && callback(currentNode.value)) {
        return currentNode;
      }

      if (value !== undefined && this.compare.equal(currentNode.value, value)) {
        return currentNode;
      }

      currentNode = currentNode.next;
    }

    return null;
  }

  deleteTail () {
    const deleteTail = this.tail;

    if (this.head === deleteTail) {
      this.head = null;
      this.tail = null;

      return deleteTail;
    }

    let currentNode = this.head;
    while (currentNode.next) {
      if (!currentNode.next.next) {
        currentNode.next = null;
      } else {
        currentNode = currentNode.next;
      }
    }

    this.tail = currentNode;

    return deleteTail;
  }

  deleteHead () {
    if (!this.head) {
      return null
    }

    const deleteHead = this.head;

    if (this.head.next) {
      this.head = this.head.next;
    } else {
      this.head = null;
      this.tail = null;
    }

    return deleteHead;
  }

  fromArray (values) {
    values.forEach(value => this.append(value));

    return this;
  }

  toArray () {
    const nodes = [];

    let currentNode = this.head;
    while (currentNode) {
      nodes.push(currentNode);
      currentNode = currentNode.next;
    }

    return nodes;
  }

  toString (callback) {
    return this.toArray().map(node => node.toString(callback)).toString();
  }

  reverse () {
    let currNode = this.head;
    let prevNode = null;
    let nextNode = null;

    while (currNode) {
      nextNode = currNode.next;
      currNode.next = prevNode;
      prevNode = currNode;
      currNode = nextNode;
    }

    this.tail = this.head;
    this.head = prevNode;

    return this;
  }
}
```
</details>

## 引用的部分
- [Comparator](../utils/comparator.md)

## 复杂度
### 时间复杂度
| Access    | Search    | Insertion | Deletion  |
| :-------: | :-------: | :-------: | :-------: |
| O(n)      | O(n)      | O(1)      | O(n)      |

### 空间复杂度
O(n)