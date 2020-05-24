# 双链表(Doubly Linked List)
**双链表** 是在单链表的基础上拓展了一个记录上个节点的指针。你也可以将其视为由两条节点相同，但 下一个(`next`) 指向相反的的单链表组成。双链表开始和结束的节点的上一个(`prev`)和下一个(`next`)链接则只会指向某种终止符(`null`)。如果双链表只有一个节点时，则这个节点无论 `next` 还是 `prev` 都指向的是自己。

## 代码剖析

### 子节点
```js
export default class DoublyLinkedListNode {
  constructor (value, next = null, previous = null) {
    this.value = value;
    this.next = next;
    // 多了个 this.previous 是和单链表的区别之处
    // 顾名思义是该节点对上一个节点的引用，初始化时可以为空
    this.previous = previous;
  }

  toString (callback) {
    return callback ? callback(this.value) : `${this.value}`;
  }
}
```

### 双链表
[Comparator 剖析](../utils/comparator.md)

```js
import DoublyLinkedListNode from './DoublyLinkedListNode';
import Comparator from '../../utils/comparator';

class DoublyLinkedList {
  constructor (comparatorFunction) {
    this.head = null; // 双链表头
    this.tail = null; // 双链表尾

    this.compare = new Comparator(comparatorFunction);
  }

  /**
   * 往头部追加一个节点
   * @param {*} value 新增节点的值
   * @return {DoublyLinkedList}
   */
  prepend (value) {
    const newNode = new DoublyLinkedListNode(value, this.head); // 因为是往头部加节点，即意味着新增节点的 this.next 要指向当前的头部节点

    if (this.head) {
      // 将当前头部节点的 this.previous 指向新增的节点
      this.head.previous = newNode;
    }
    this.head = newNode;
    
    if (!this.tail) {
      // 如果当前双链表的尾部为空，则新增的节点同样也是尾部节点
      this.tail = newNode;
    }

    return this;
  }

  /**
   * 往尾部追加一个节点
   * @param {*} value 新增节点的值
   * @return {DoublyLinkedList}
   */
  append (value) {
    const newNode = new DoublyLinkedListNode(value);

    if (!this.head) {
      // 如果双链表的头部节点为空，则意味着这是一个空的链表，因此将头部和尾部都指向新增节点
      this.head = newNode;
      this.tail = newNode;

      return this;
    }

    // 当前尾部的节点的 this.next 指向新增节点
    this.tail.next = newNode;
    // 新增节点的 this.previous 指向当前尾部节点
    newNode.previous = this.tail;
    // 将链表的尾部指向新增的节点
    this.tail = newNode;

    return this;
  }

  /**
   * 根据value删除 value与之相同的节点
   * @param {*} value
   * @return {DoublyLinkedListNode}
   */
  delete (value) {
    if (!this.head) {
      // 头部都没有的话，只能是个空链表，因此返回null
      return null;
    }

    let deleteNode = null; // 待删除的节点
    let currentNode = this.head; // 当前遍历的节点

    while (currentNode) {
      if (this.compare.equal(currentNode.value, value)) {
        // 当前遍历的节点的值和待删除节点的值相等，进入删除逻辑
        // 将其赋值给 待删除的节点
        deleteNode = currentNode;

        // 开始考虑各种边界情况
        if (deleteNode === this.head) {
          // 如果 待删除的节点 就是当前的头部节点的话
          // 将头部指向 待删除的节点 的 this.next
          this.head = deleteNode.next;

          if (this.head) {
            // 改变了指向的 this.head 如果存在的话
            // 需要将其 this.previous 置空
            this.head.previous = null;
          }

          if (deleteNode === this.tail) {
            // 若此时 待删除的节点 亦是尾部的话
            // 那么意味着该链表只剩下这唯一一个节点了
            // 将尾部置空
            this.tail = null;
          }
        } else if (deleteNode === this.tail) {
          // 若 待删除的节点 是尾部的节点
          // 将链表的尾部指向 待删除的节点 的 this.previous
          this.tail = deleteNode.previous;
          // 而后将尾部节点的 this.next 置空
          this.tail.next = null;
        } else {
          // 待删除的节点 是中间的某个节点
          // 先取出其 this.next 和 this.previous
          const previousNode = deleteNode.previous;
          const nextNode = deleteNode.next;
          // 而后将其 this.next 和 this.previous 直接关联起来
          // 而这个未被引用的节点最终会被GC干掉
          previousNode.next = nextNode;
          nextNode.previous = previousNode;
        }
      }
        
      // 遍历的当前节点的 this.next 下一个节点
      currentNode = currentNode.next;
    }

    return deleteNode; // 返回被删除的节点
  }
}
```