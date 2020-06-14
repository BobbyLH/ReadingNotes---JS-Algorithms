# 队列(Queue)
**队列** 是一种特殊的数据结构，它严格遵循“先进先出(First-In-First-Out FIFO)”的原则，添加到队列中的操作被称为入队(enqueue)，移除出队列的操作被称为出队(dequeue)。通常还会支持一些诸如 *查看最前面要出队元素(peek)* 的操作，但不会将元素移除出队列。可以将队列理解成是一个线性的数据结构，或者更抽象地说是一个顺序结合。

## 代码剖析

[链表](./Linked-List.md)

```js
import LinkedList from './LinkedList';

export default class Queue {
  constructor () {
    // 基于已有的单链表构造队列，而不是重复造轮子是因为它们有相似的构造
    // 也就是说，它们大部分都是从头至尾的操作元素，比如 单链表 的 append 和 deleteHead 之于 队列的 enqueue 和 dequeue
    this.linkedList = new LinkedList();
  }

  /**
   * 检查 Queue 是否为空
   * @return {boolean}
   */
  isEmpty () {
    // 连 this.head 都没有，证明队列是空的
    return !this.linkedList.head;
  }

  /**
   * 读取排在队列头上的元素的值
   * @return {any}
   */
  peek () {
    // 空队列
    if (this.isEmpty()) return null;

    return this.linkedList.head.value;
  }

  /**
   * 入队，即将元素添加至队列尾巴处(链表的 this.tail)
   * 按照 FIFO 的规则，要等到前面其他元素都出队后，该元素才能出队
   * @param {any} value
   */
  enqueue (value) {
    this.linkedList.append(value);
  }

  /**
   * 出队，即将队列的第一个元素移除(链表的 this.head)
   * @return {any}
   */
  dequeue () {
    const removedHead = this.linkedList.deleteHead();
    return removedHead ? removedHead.value : null;
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
