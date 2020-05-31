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
   * @param {any} value 新增节点的值
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
   * @param {any} value 新增节点的值
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
   * @param {any} value
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

  /**
   * 根据value找出对应的节点
   * @param {{ value: any; callback?: (value: any) => boolean; }} findParams
   * @return {DoublyLinkedListNode}
   */
  find ({
     value = undefined,
     callback = undefined
   }) {
    if (!this.head) {
      // 没有 this.head 证明链表内没有任何节点
      return null;
    }

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

  /**
   * 删除最后一个节点
   * @return {DoublyLinkedListNode}
   */
  deleteTail () {
    if (!this.head) {
      // 没有 this.head 证明链表内没有任何节点
      return null
    }

    const deleteTail = this.tail; // 将当前 this.tail 保存起来
    this.tail = this.tail.previous; // 将当前 this.tail 的上一个节点 previous 赋值给 this.tail
    this.tail.next = null; // 将新的 this.tail 的 next 置空

    return deleteTail;
  }

  /**
   * 删除第一个节点
   * @return {DoublyLinkedListNode}
   */
  deleteHead () {
    if (!this.head) {
      // 没有 this.head 证明链表内没有任何节点
      return null
    }

    const deleteHead = this.head; // 将当前 this.head 保存起来

    if (this.head.next) {
      this.head = this.tail.next; // 将当前 this.head 的下一个节点 next 赋值给 this.head
      this.tail.previous = null; // 将新的 this.head 的 previous 置空
    } else {
      // 边界情况：若当前 this.head 是唯一的节点的话
      this.head = null;
      this.tail = null;
    }

    return deleteHead;
  }

  /**
   * 将链表的所有节点组装成一个数组后返回
   * @return {DoublyLinkedListNode[]}
   */
  toArray (values) {
    const nodes = [];

    let currentNode = this.head;
    while(currentNode) {
      nodes.push(currentNode);
      currentNode = currentNode.next;
    }

    return nodes;
  }

  /**
   * 将一组数据添加为节点
   * @param {any[]} values - 一组需要被添加成节点的值
   * @return {DoublyLinkedList}
   */
  fromArray (values) {
    values.forEach(value => this.append(value));

    return this;
  }

  /**
   * 将链表中所有节点的值转换成字符串
   * @param {(value: any) => string} [callback]
   * @return {string}
   */
  toString (callback) {
    return this.toArray().map(node => node.toString(callback)).toString();
  }

  /**
   * 将链表中节点的顺序完全颠倒
   * @return {DoublyLinkedList}
   */
  reverse () {
    let currNode = this.head; // 先从 this.head 开始
    let prevNode = null;
    let nextNode = null;

    while (currNode) {
      // 将 next 和 previous 指向的节点分别保存下来
      nextNode = currNode.next;
      prevNode = currNode.previous;

      // 而后交换它们的位置
      currNode.next = prevNode;
      currNode.previous = nextNode;

      // 每次保存当前节点，直到最后一个节点，而后会将这个节点赋值给 this.head
      prevNode = currNode;
      // 遍历下一个节点
      currNode = nextNode;
    }

    this.tail = this.head;
    this.head = prevNode;

    return this;
  }
}
```

## 复杂度
### 时间复杂度
| Access    | Search    | Insertion | Deletion  |
| :-------: | :-------: | :-------: | :-------: |
| O(n)      | O(n)      | O(1)      | O(n)      |

### 空间复杂度
O(n)