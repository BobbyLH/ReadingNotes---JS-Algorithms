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

### 链表
[Comparator 剖析](../utils/comparator.md)

```js
import LinkedListNode from './LinkedListNode';
import Comparator from '../../utils/comparator';

class LinkedList {
  constructor (compareFunction) {
    this.head = null; // 链表的头
    this.tail = null; // 链表的尾

    this.compare = new Comparator(compareFunction);
  }

  /**
   * 往头部追加一个节点
   * @param {any} value 新增节点的值
   * @return {LinkedList}
   */
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

  /**
   * 往尾部追加一个节点
   * @param {any} value 新增节点的值
   * @return {LinkedList}
   */
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

  /**
   * 删除指定值的节点
   * @param {any} value 删除节点的值
   * @return {LinkedListNode}
   */
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

  /**
   * @param {Object} findParams
   * @param {*} findParams.value
   * @param {function} [findParams.callback]
   * @return {LinkedListNode}
   */
}
```

## 复杂度
### 时间复杂度
| Access    | Search    | Insertion | Deletion  |
| :-------: | :-------: | :-------: | :-------: |
| O(n)      | O(n)      | O(1)      | O(n)      |

### 空间复杂度
O(n)