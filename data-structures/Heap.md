# 堆(Heap)
**堆** 是一个树形的数据结构，且必须满足：

  1. 小堆，若是 `P` 节点 是 `C` 节点 的父级，那么 `P` 必须小于等于 `C` 的 key；

  2. 大堆，相同的条件，`P` 必须大于等于 `C` 的 key；

  3. 最顶部，没有父节点的节点，被称为“根节点”。

## 代码剖析

[Comparator 剖析](../utils/comparator.md)
### 堆
```js
import Comparator from '../../utils/comparator';

export default class Heap {
  constructor (comparatorFunction) {
    if (new.target === Heap) throw new TypeError('不能直接构造Heap的实例，请构造 大/小 堆');

    this.heapContainer = []; // 用以保存堆的元素
    this.compare = new Comparator(comparatorFunction);
  }

  /**
   * 根据父节点的索引，获取左侧子节点的索引
   * @param {number} parentIndex 父节点的索引
   * @return {number}
   */
  getLeftChildIndex (parentIndex) {
    // 比右侧子节点小1
    return (2 * parentIndex) + 1;
  }

  /**
   * 根据父节点的索引，获取右侧子节点的索引
   * @param {number} parentIndex 父节点的索引
   * @return {number}
   */
  getRightChildIndex (parentIndex) {
    // 比左侧子节点大1
    return (2 * parentIndex) + 2;
  }

  /**
   * 根据子节点的索引，获取父节点的索引
   * @param {number} childIndex 子节点的索引
   * @return {number}
   */
  getParentIndex (childIndex) {
    // 向下取整，即为父节点的索引
    return Math.floor((childIndex - 1) / 2);
  }

  /**
   * 根据子节点的索引，判断父节点是否存在
   * @param {number} childIndex 子节点的索引
   * @return {boolean}
   */
  hasParent (childIndex) {
    return this.getParentIndex(childIndex) >= 0;
  }

  /**
   * 根据父节点的索引，判断左侧子节点是否存在
   * @param {number} parentIndex 父节点的索引
   * @return {boolean}
   */
  hasLeftChild (parentIndex) {
    return this.getLeftChildIndex(parentIndex) < this.heapContainer.length;
  }

  /**
   * 根据父节点的索引，判断右侧子节点是否存在
   * @param {number} parentIndex 父节点的索引
   * @return {boolean}
   */
  hasRightChild (parentIndex) {
    return this.getRightChildIndex(parentIndex) < this.heapContainer.length;
  }

  /**
   * 根据父节点的索引，获取左侧子节点
   * @param {number} parentIndex 父节点的索引
   * @return {any}
   */
  leftChild (parentIndex) {
    return this.heapContainer[getLeftChildIndex(parentIndex)];
  }

  /**
   * 根据父节点的索引，获取右侧子节点
   * @param {number} parentIndex 父节点的索引
   * @return {any}
   */
  rightChild (parentIndex) {
    return this.heapContainer[getRightChildIndex(parentIndex)];
  }

  /**
   * 根据子节点的索引，获取父节点
   * @param {number} childIndex 子节点的索引
   * @return {any}
   */
  parent (childIndex) {
    return this.heapContainer[getParentIndex(parentIndex)];
  }

  /**
   * 交换两个节点的位置
   * @param {number} indexOne 节点一的位置
   * @param {number} indexTwo 节点二的位置
   */
  swap (indexOne, indexTwo) {
    const tmp = this.heapContainer[indexTwo];
    this.heapContainer[indexTwo] = this.heapContainer[indexOne];
    this.heapContainer[indexOne] = tmp;
  }

  /**
   * 在不修改数据结构的情况下，返回根节点
   * @return {any} 根节点
   */
  peek () {
    if (this.heapContainer.length === 0) {
      // 没有任何节点
      return null;
    }

    return this.heapContainer[0];
  }

  /**
   * 将颠倒头尾，在重新调整数据结构后，并返回根节点
   * @return {any} 根节点
   */
  poll () {
    if (this.heapContainer.length === 0) {
      // 没有任何节点
      return null;
    }

    if (this.heapContainer.length === 1) {
      //　只有根节点
      return this.heapContainer.pop();
    }

    const item = this.heapContainer[0];

    // 将最后的一个元素移到第一位
    this.heapContainer[0] = this.heapContainer.pop();
    // 从头到尾重新排序
    this.heapifyDown();

    return item;
  }

  /**
   * 添加节点到堆中
   * @param {any} 添加的节点
   * @return {Heap} 返回整个堆
   */
  add (item) {
    this.heapContainer.push(item);
    // 从尾到头重新排序
    this.heapifyUp();
    return this;
  }
}
```

### 小堆
```js
import Heap from './Heap';

export default class MinHeap extends Heap {
  /**
   * 小堆 —— 第一个元素总是小于或等于第二个元素
   * @params {any} firstElement
   * @params {any} secondElement
   * @return {boolean}
   */
  pairIsInCorrectOrder (firstElement, secondElement) {
    return this.compare.lessThannOrEqual(firstElement, secondElement);
  }
}
```

### 大堆
```js
import Heap from './Heap';

export default class MaxHeap extends Heap {
  /**
   * 大堆 —— 第一个元素总是大于或等于第二个元素
   * @params {any} firstElement
   * @params {any} secondElement
   * @return {boolean}
  */
  pairIsInCorrectOrder (firstElement, secondElement) {
    return this.compare.greaterThanOrEqual(firstElement, secondElement);
  }
}
```