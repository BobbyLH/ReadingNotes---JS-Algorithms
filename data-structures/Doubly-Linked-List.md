# 双链表(Doubly Linked List)
**双链表** 是在单链表的基础上拓展了一个记录上个节点的指针。你也可以将其视为由两条节点相同，但 下一个(`next`) 指向相反的的单链表组成。双链表开始和结束的节点的上一个(`prev`)和下一个(`next`)链接则只会指向某种终止符(`null`)。如果双链表只有一个节点时，则这个节点无论 `next` 还是 `prev` 都指向的是自己。

## 代码剖析
<details open>
<summary>收起 / 展开</summary>

### DoublyLinkedListNode 类 - 节点
- 用 `this.value` 储存了节点的值

- 用 `this.next` 保存下一个节点的地址

- 实现了一个 `toString` 的方法，用于获取节点的值

  ```js
  class DoublyLinkedListNode {
    constructor (value, next = null, previous = null) {
      this.value = value; // 节点所包含的数据
      this.next = next; // 节点对下一个节点的引用，初始化时可以为空
      
      // 多了个 this.previous 是和单链表的区别之处
      this.previous = previous; // 顾名思义是该节点对上一个节点的引用，初始化时可以为空
    }

    // 获取节点数据的方法
    // 可以接受一个回调函数，并将节点包含的数据作为参数，否则直接返回隐式转换成字符串类型的数据
    toString (callback) {
      return callback ? callback(this.value) : `${this.value}`;
    }
  }
  ```

### DoublyLinkedListNode 类 - 双链表
- 用 `this.head` 保存双链表的头部节点

- 用 `this.tail` 保存双链表的尾部节点

- 实现了一个 `compare` 的方法，用于对比节点的值

  ```js
  class DoublyLinkedList {
    constructor (comparatorFunction) {
      this.head = null; // 双链表头
      this.tail = null; // 双链表尾

      this.compare = new Comparator(comparatorFunction);
    }
  }
  ```

#### prepend - 往头部追加一个节点
- 实例化一个 `DoublyLinkedListNode`，并将其 “插入” 到链表的头部

- 入参(Params)：
    - `value` <any> 待新增的节点的值

- 出参(Returns)：<DoublyLinkedList> 返回链表的整个实例

  ```js
  prepend (value) {
    // 因为是往头部加节点，即意味着新增节点的 this.next 要指向当前的头部节点
    const newNode = new DoublyLinkedListNode(value, this.head);

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
  ```

#### append - 往尾部追加一个节点
- 实例化一个 `DoublyLinkedListNode`，并将其 “插入” 到链表的尾部

- 入参(Params)：
    - `value` <any> 待新增的节点的值

- 出参(Returns)：<DoublyLinkedList> 返回链表的整个实例

  ```js
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
  ```

#### delete - 根据value删除节点
- 在一次循环内，使用 `this.compare.equal` 方法，根据传入的值匹配需要删除的所有节点

- 入参(Params)：
    - `value` <any> 待删除的节点的值

- 出参(Returns)：<DoublyLinkedListNode> 返回删除的节点

  ```js
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
  ```

#### find - 根据value找出对应的节点
- 在一次循环内，使用 自定义的回调函数 `callback` 或 `this.compare.equal` 方法，找出和参数 value 匹配的节点

- 入参(Params)：
    - `findParams` <{ value: any; callback?: (value: any) => boolean; }> 查找的参数

- 出参(Returns)：<DoublyLinkedListNode> 返回匹配的节点

  ```js
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
  ```

#### deleteTail - 删除最后一个节点
- 删除完成后会重新绑定 `this.tail`

- 出参(Returns)：<DoublyLinkedListNode> 删除的节点

  ```js
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
  ```

#### deleteHead - 删除第一个节点
- 删除完成后会重新绑定 `this.head`

- 出参(Returns)：<DoublyLinkedListNode> 删除的节点

  ```js
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
  ```

#### fromArray - 将一组值作为节点添加到双链表后
- 遍历传入的值，并调用 `this.append` 将值作为节点添加到链表后

- 入参(Params)：
    - `values` <any[]> 一组需要被添加成节点的值

- 出参(Returns)：<DoublyLinkedList> 返回整个实例化的双链表

  ```js
  fromArray (values) {
    values.forEach(value => this.append(value));

    return this;
  }
  ```

#### toArray - 将双链表的所有节点组装成一个数组后返回
- 从链表头部开始，逐个遍历链表的所有节点，并组合成一个数组后返回

- 出参(Returns)：<DoublyLinkedListNode[]> 返回整个实例化的链表

  ```js
  toArray (values) {
    const nodes = [];

    let currentNode = this.head;
    while (currentNode) {
      nodes.push(currentNode);
      currentNode = currentNode.next;
    }

    return nodes;
  }
  ```

#### toString - 将双链表中所有节点的值转换成字符串
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

#### reverse - 将双链表中节点的顺序完全颠倒
- 本质上来看，就是通过改变每个节点的 `this.next`  和 `this.previous` 属性，实现了颠倒，当然要记得将双链表的 `this.head` 和 `this.tail` 重新赋值

- 出参(Returns)：<DoublyLinkedList> 返回整个实例化的链表

  ```js
  reverse () {
    // 先从 this.head 开始
    let currNode = this.head;
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
export default class DoublyLinkedListNode {
  constructor (value, next = null, previous = null) {
    this.value = value;
    this.next = next;
    this.previous = previous;
  }

  toString (callback) {
    return callback ? callback(this.value) : `${this.value}`;
  }
}
```

### 双链表
```js
import DoublyLinkedListNode from './DoublyLinkedListNode';
import Comparator from '../../utils/comparator';

class DoublyLinkedList {
  constructor (comparatorFunction) {
    this.head = null;
    this.tail = null;

    this.compare = new Comparator(comparatorFunction);
  }

  prepend (value) {
    const newNode = new DoublyLinkedListNode(value, this.head);

    if (this.head) {
      this.head.previous = newNode;
    }
    this.head = newNode;
    
    if (!this.tail) {
      this.tail = newNode;
    }

    return this;
  }

  append (value) {
    const newNode = new DoublyLinkedListNode(value);

    if (!this.head) {
      this.head = newNode;
      this.tail = newNode;

      return this;
    }

    this.tail.next = newNode;
    newNode.previous = this.tail;
    this.tail = newNode;

    return this;
  }

  delete (value) {
    if (!this.head) {
      return null;
    }

    let deleteNode = null;
    let currentNode = this.head;

    while (currentNode) {
      if (this.compare.equal(currentNode.value, value)) {
        deleteNode = currentNode;

        if (deleteNode === this.head) {
          this.head = deleteNode.next;

          if (this.head) {
            this.head.previous = null;
          }

          if (deleteNode === this.tail) {
            this.tail = null;
          }
        } else if (deleteNode === this.tail) {
          this.tail = deleteNode.previous;
          this.tail.next = null;
        } else {
          const previousNode = deleteNode.previous;
          const nextNode = deleteNode.next;
          previousNode.next = nextNode;
          nextNode.previous = previousNode;
        }
      }

      currentNode = currentNode.next;
    }

    return deleteNode;
  }

  find ({
     value = undefined,
     callback = undefined
   }) {
    if (!this.head) {
      return null;
    }

    let currentNode = this.head;

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
    if (!this.head) {
      return null
    }

    const deleteTail = this.tail;
    this.tail = this.tail.previous;
    this.tail.next = null;

    return deleteTail;
  }

  deleteHead () {
    if (!this.head) {
      return null
    }

    const deleteHead = this.head;

    if (this.head.next) {
      this.head = this.tail.next;
      this.tail.previous = null;
    } else {
      this.head = null;
      this.tail = null;
    }

    return deleteHead;
  }

  toArray (values) {
    const nodes = [];

    let currentNode = this.head;
    while (currentNode) {
      nodes.push(currentNode);
      currentNode = currentNode.next;
    }

    return nodes;
  }

  fromArray (values) {
    values.forEach(value => this.append(value));

    return this;
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
      prevNode = currNode.previous;

      currNode.next = prevNode;
      currNode.previous = nextNode;

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