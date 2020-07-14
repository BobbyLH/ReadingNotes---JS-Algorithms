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

    this.heapContainer = []; // 用以保存堆节点的容器
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
    // 左侧节点的索引值 小于 堆容器中节点的总数
    return this.getLeftChildIndex(parentIndex) < this.heapContainer.length;
  }

  /**
   * 根据父节点的索引，判断右侧子节点是否存在
   * @param {number} parentIndex 父节点的索引
   * @return {boolean}
   */
  hasRightChild (parentIndex) {
    // 右侧节点的索引值 小于 堆容器中节点的总数
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
   * @return {any} 堆的根节点
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
   * @return {any} 堆的根节点
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

    // 将最后的一个节点移到第一位
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

  /**
   * 根据传入的节点，删除其在堆中所对应的全部节点
   * @param {any} item 删除的节点
   * @param {Comparator} comparator 比较节点是否相等的方法
   * @return {Heap} 返回整个堆
   */
  remove (item, comparator = this.compare) {
    // 找出堆中所对应的待删除节点的总长度
    const numberOfItemToRemove = this.find(item, comparator).length;

    for (let i = 0; iteration < numberOfItemToRemove; iteration += 1) {
      // 某个需要移除的节点所对应的索引
      const indexToRemove = this.find(item, comparator).pop();

      if (indexToRemove === (this.heapContainer.length - 1)) {
        // 是最末尾的节点，则直接删除
        this.heapContainer.pop();
      } else {
        // 将最后一个节点赋移到被删除的节点的位置
        this.heapContainer[indexToRemove] = this.heapContainer.pop();

        // 被删除的节点的父节点
        const parentItem = this.parent(indexToRemove);

        if (
            this.hasLeftChild(indexToRemove) && (
              !parentItem ||
              this.pairIsInCorrectOrder(
                parentItem,
                this.heapContainer[indexToRemove]
              )
            )
           )
        {
          // 被删除的节点的存在左侧的子节点 且
          // 没有父节点/是根节点 或是 被删除节点的父节点和其位置正确
          // 至上而下进行重新排序
          this.heapifyDown();
        } else {
          // 都不满足，可以推断被删除的节点要么是叶子节点，要么它和父节点的位置本身就不正确
          // 选择至下而上进行重新排序
          this.heapifyUp();
        }
      }
    }

    return this;
  }

  /**
   * 根据传入的节点，找出其在堆中所对应的节点的全部索引值
   * @param {any} item 被用于查找的节点
   * @param {Comparator} comparator 比较节点是否相等的方法
   * @return {number[]} 返回符合条件节点的索引
   */
  find (item, comparator = this.compare) {
    const foundItemIndices = [];

    for (let itemIndex = 0; itemIndex < this.heapContainer.length; itemIndex += 1) {
      if (comparator.equal(item, this.heapContainer[itemIndex])) {
        foundItemIndices.push(itemIndex);
      }
    }

    return foundItemIndices;
  }

  /**
   * 是否是空堆
   * @return {boolean}
   */
  isEmpty() {
    return !this.heapContainer.length;
  }

  /**
   * 返回堆的字符串
   * @return {string}
   */
  toString() {
    return this.heapContainer.toString();
  }

  /**
   * 至下而上的重新排序
   * 将子节点正确的调整到父节点的位置
   * @param {number} customStartIndex 自定义起始索引
   */
  heapifyUp(customStartIndex) {
    // 起始的索引，可自定义，默认从最后一个节点开始
    let currentIndex = customStartIndex || this.heapContainer.length - 1;

    // 从起始的索引开始遍历堆
    // 当前节点不是根节点 且 当前节点和其父节点的位置不正确
    // 进入到排序逻辑
    while (
      this.hasParent(currentIndex)
      && !this.pairIsInCorrectOrder(this.parent(currentIndex), this.heapContainer[currentIndex])
    ) {
      // 交换当前节点和其父节点的位置
      this.swap(currentIndex, this.getParentIndex(currentIndex));
      // 继续排查
      // 从已经交换过后的当前节点的新的父节点开始 
      currentIndex = this.getParentIndex(currentIndex);
    }
  }

  /**
   * 至上而下的重新排序
   * 将前面的节点正确的调整到后面的位置
   * @param {number} customStartIndex 自定义起始索引
   */
  heapifyDown(customStartIndex = 0) {
    // 起始的索引，可自定义，默认从根节点开始
    let currentIndex = customStartIndex;
    // 对比节点的索引
    let nextIndex = null;

    // 当前节点有左侧子节点
    while (this.hasLeftChild(currentIndex)) {
      if (
        this.hasRightChild(currentIndex)
        && this.pairIsInCorrectOrder(
          this.rightChild(currentIndex),
          this.leftChild(currentIndex)
        )
      ) {
        // 若是右侧也有子节点，且该左右两节点的位置正确
        // 将右侧子节点作为和父节点比较的节点
        // 保存索引
        nextIndex = this.getRightChildIndex(currentIndex);
      } else {
        // 将左侧子节点作为和父节点比较的节点
        // 保存索引
        nextIndex = this.getLeftChildIndex(currentIndex);
      }

      if (this.pairIsInCorrectOrder(
        this.heapContainer[currentIndex],
        this.heapContainer[nextIndex]
      )) {
        // 若是位置正确，则跳出遍历
        break;
      }

      // 位置不正确，交换它们的位置
      this.swap(currentIndex, nextIndex);
      // 继续排查
      // 从已经交换过后的子节点，即之前的父节点开始 
      currentIndex = nextIndex;
    }
  }

  /**
   * 检查堆的两个节点之间的位置是否正确
   * 应被子类改写
   * @param {any} firstElement
   * @param {any} secondElement
   * @return {boolean}
   */
  pairIsInCorrectOrder(firstElement, secondElement) {
    // 直接调用父类的该方法会报错
    // 应利用多态，从而被子类改写
    throw new Error('请定义子类的 pairIsInCorrectOrder 方法!');
  }
}
```

### 小堆
```js
import Heap from './Heap';

export default class MinHeap extends Heap {
  /**
   * 检查堆的两个节点之间的位置是否正确
   * 小堆 —— 第一个节点总是小于或等于第二个节点
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
   * 检查堆的两个节点之间的位置是否正确
   * 大堆 —— 第一个节点总是大于或等于第二个节点
   * @params {any} firstElement
   * @params {any} secondElement
   * @return {boolean}
  */
  pairIsInCorrectOrder (firstElement, secondElement) {
    return this.compare.greaterThanOrEqual(firstElement, secondElement);
  }
}
```