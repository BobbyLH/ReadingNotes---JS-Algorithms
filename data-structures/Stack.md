# 栈(Stack)
**栈(Stack)** 是一种用于表示元素集合的抽象的数据结构，对它的操作遵循两个原则：

- **push(入栈)**，将元素加入到集合中

- **pop(出栈)**，将最新入栈的元素移除集合

总结下来就是 LIFO(last in, first out)。“栈” 这个词也很形象的描述了一个物理的结构 —— 从一个堆叠了一大堆东西的头部取出某个东西很容易，但若是要拿出最底下的东西，那就要把很多在它之上的东西先取出来。

## 代码剖析

[链表](./Linked-List.md)

```js
import LinkedList from './LinkedList';

export default class Stack {
  constructor () {
    // 基于已有的单链表构造栈，而不是重复造轮子是因为它们有相似的构造
    // 也就是说，它们大部分都是从头至尾的操作元素，比如 单链表 的 prepend 和 deleteHead 之于 栈的 push 和 pop
    this.linkedList = new LinkedList();
  }

  /**
   * 检查 Stack 是否为空
   * @return {boolean}
   */
  isEmpty () {
    // 连 this.head 都没有，证明栈是空的
    return !this.linkedList.head;
  }

  /**
   * 读取排在栈头上的元素的值
   * @return {any}
   */
  peek () {
    // 空栈
    if (this.isEmpty()) return null;

    return this.linkedList.head.value;
  }

  /**
   * 入栈，即将元素插入到链表的头部
   * @param {any} value
   */
  push (value) {
    this.linkedList.prepend(value);
  }

  /**
   * 出栈，即将链表头部的元素删除掉
   * @return {any}
   */
  pop () {
    const removeHead = this.linkedList.deleteHead();
    return removeHead ? removeHead.value : null;
  }

  /**
   * 将栈的元素的数据转化成数组
   * 即遍历链表的每个元素，而后取出其value塞到数组中
   * @return {any[]}
   */
  toArray () {
    return this.linkedList.toArray().map(linkListNode => linkListNode.value);
  }

  /**
   * @param {(value: any) => string} [callback]
   * @return {string}
   */
  toString (callback) {
    return this.linkedList.toString(callback);
  }
}
```