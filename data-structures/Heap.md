# 堆(Heap)
**堆** 是一个树形的数据结构，且必须满足：

  1. 小堆，若是 `P` 节点 是 `C` 节点 的父级，那么 `P` 必须小于等于 `C` 的 key；

  2. 大堆，相同的条件，`P` 必须大于等于 `C` 的 key；

  3. 最顶部，没有父节点的节点，被称为“根节点”。

## 代码剖析
<details open>
<summary>收起 / 展开</summary>

### Heap 类 - 堆
- 使用数组实现即可

- 无法直接构造 `Heap` 的实例，需要指定是 *大堆* 还是 *小堆*

- 入参：
    - `comparatorFunction` <Comparator> 自定义的比较函数

  ```js
  class Heap {
    constructor (comparatorFunction) {
      if (new.target === Heap) throw new TypeError('不能直接构造Heap的实例，请构造 大/小 堆');

      this.heapContainer = []; // 用以保存堆节点的容器
      this.compare = new Comparator(comparatorFunction);
    }
    //……
  }
  ```

#### getLeftChildIndex - 根据父节点的索引，获取左侧子节点的索引
- 入参：
    - `parentIndex` <number> 父节点的索引

- 出参(Returns)：<number> 左侧子节点的索引，比右侧子节点小1

  ```js
    getLeftChildIndex (parentIndex) {
      return (2 * parentIndex) + 1;
    }
  ```

#### getRightChildIndex - 根据父节点的索引，获取右侧子节点的索引
- 入参：
    - `parentIndex` <number> 父节点的索引

- 出参(Returns)：<number> 右侧子节点的索引，比左侧子节点大1

  ```js
    getRightChildIndex (parentIndex) {
      return (2 * parentIndex) + 2;
    }
  ```

#### getParentIndex - 根据子节点的索引，获取父节点的索引
- 入参：
    - `childIndex` <number> 子节点的索引

- 出参(Returns)：<number> 父节点的索引，向下取整

  ```js
    getParentIndex (childIndex) {
      return Math.floor((childIndex - 1) / 2);
    }
  ```

#### hasParent - 根据子节点的索引，判断父节点是否存在
- 判断的依据是：父节点的索引是否存在

- 入参：
    - `childIndex` <number> 子节点的索引

- 出参(Returns)：<boolean> 父节点是否存在

  ```js
    hasParent (childIndex) {
      return this.getParentIndex(childIndex) >= 0;
    }
  ```

#### hasLeftChild - 根据父节点的索引，判断左侧子节点是否存在
- 判断的依据是：左侧节点的索引值 是否小于 堆容器中节点的总数

- 入参：
    - `parentIndex` <number> 父节点的索引

- 出参(Returns)：<boolean> 左侧子节点是否存在

  ```js
    hasLeftChild (parentIndex) {
      return this.getLeftChildIndex(parentIndex) < this.heapContainer.length;
    }
  ```

#### hasRightChild - 根据父节点的索引，判断左侧子节点是否存在
- 判断的依据是：右侧节点的索引值 是否小于 堆容器中节点的总数

- 入参：
    - `parentIndex` <number> 父节点的索引

- 出参(Returns)：<boolean> 右侧子节点是否存在

  ```js
    hasRightChild (parentIndex) {
      return this.getRightChildIndex(parentIndex) < this.heapContainer.length;
    }
  ```

#### leftChild - 根据父节点的索引，获取左侧子节点
- 先获取左侧子节点的索引值

- 入参：
    - `parentIndex` <number> 父节点的索引

- 出参(Returns)：<any> 左侧子节点

  ```js
    leftChild (parentIndex) {
      return this.heapContainer[getLeftChildIndex(parentIndex)];
    }
  ```

#### rightChild - 根据父节点的索引，获取右侧子节点
- 先获取右侧子节点的索引值

- 入参：
    - `parentIndex` <number> 父节点的索引

- 出参(Returns)：<any> 右侧子节点

  ```js
    rightChild (parentIndex) {
      return this.heapContainer[getRightChildIndex(parentIndex)];
    }
  ```

#### parent - 根据子节点的索引，获取父节点
- 先获取父节点的索引值

- 入参：
    - `childIndex` <number> 子节点的索引

- 出参(Returns)：<any> 父节点

  ```js
    parent (childIndex) {
      return this.heapContainer[getParentIndex(parentIndex)];
    }
  ```

#### swap - 交换两个节点的位置
- 入参：
    - `indexOne` <number> 节点一的位置

    - `indexTwo` <number> 节点二的位置

- 出参(Returns)：<undefined>

  ```js
    swap (indexOne, indexTwo) {
      const tmp = this.heapContainer[indexTwo];
      this.heapContainer[indexTwo] = this.heapContainer[indexOne];
      this.heapContainer[indexOne] = tmp;
    }
  ```

#### peek - 在不修改数据结构的情况下，返回根节点
- 出参(Returns)：<any> 堆的根节点 或 则 `null`

  ```js
    peek () {
      if (this.heapContainer.length === 0) {
        // 没有任何节点
        return null;
      }

      return this.heapContainer[0];
    }
  ```

#### poll - 将颠倒头尾，在重新调整数据结构后，并返回根节点
- 出参(Returns)：<any> 堆的根节点 或 则 `null`

  ```js
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
  ```

#### add - 添加节点到堆中
- 入参：
    - `item` <any> 待添加的节点

- 出参(Returns)：<Heap> 返回整个堆

  ```js
    add (item) {
      this.heapContainer.push(item);
      // 从尾到头重新排序
      this.heapifyUp();
      return this;
    }
  ```

#### remove - 根据传入的节点，删除其在堆中所对应的全部节点
- 步骤：
    1. 找出堆中所对应的待删除节点的总长度

    2. 根据长度循环，挨个找出待删除的节点，并删除

    3. 返回整个全新的堆

- 入参：
    - `item` <any> 待删除的节点

    - `comparator` <Comparator> 比较节点是否相等的方法

- 出参(Returns)：<Heap> 返回整个堆

  ```js
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
  ```

#### find - 根据传入的节点，找出其在堆中所对应的节点的全部索引值
- 入参：
    - `item` <any> 被用于查找的节点

    - `comparator` <Comparator> 比较节点是否相等的方法，默认使用 `this.compare` 方法进行比较

- 出参(Returns)：<number[]> 返回符合条件节点的索引

  ```js
    find (item, comparator = this.compare) {
      const foundItemIndices = [];

      for (let itemIndex = 0; itemIndex < this.heapContainer.length; itemIndex += 1) {
        if (comparator.equal(item, this.heapContainer[itemIndex])) {
          foundItemIndices.push(itemIndex);
        }
      }

      return foundItemIndices;
    }
  ```

#### isEmpty - 是否是空堆
- 出参(Returns)：<boolean> 根据 `this.heapContainer` 的长度来判定是否是空堆

  ```js
    isEmpty() {
      return !this.heapContainer.length;
    }
  ```

#### toString - 返回堆的字符串
- 出参(Returns)：<string> 返回字符化后的堆

  ```js
    toString() {
      return this.heapContainer.toString();
    }
  ```

#### heapifyUp - 至下而上的重新排序
- 调用这个方法的前提是 **靠后的节点的位置不正确**

- 实现过程：
    - 从起始的索引开始遍历堆

    - 调用 `this.pairIsInCorrectOrder` 来判断节点的位置是否正确

    - 使用 `this.swap` 交换节点的位置

- 入参：
    - `customStartIndex` <number> 自定义起始索引

- 出参(Returns)：<undefined>

  ```js
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
  ```

#### heapifyDown - 至上而下的重新排序
- 调用这个方法的前提是 **靠前的节点的位置不正确**

- 实现过程：
    - 从起始的索引开始遍历堆

    - 调用 `this.pairIsInCorrectOrder` 来判断节点的位置是否正确

    - 使用 `this.swap` 交换节点的位置

- 入参：
    - `customStartIndex` <number> 自定义起始索引

- 出参(Returns)：<undefined>

  ```js
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
  ```

#### pairIsInCorrectOrder - 检查堆的两个节点之间的位置是否正确
- 这个方法需要利用多态的特性，在子类(大/小堆)中重写，直接调用会抛出错误

- 入参：
    - `firstElement` <any> 比较的第一个节点

    - `secondElement` <any> 比较的第二个节点

- 出参(Returns)：<boolean> 两个节点之间的位置是否正确

  ```js
    pairIsInCorrectOrder(firstElement, secondElement) {
      // 直接调用父类的该方法会报错
      // 应利用多态，从而被子类改写
      throw new Error('请定义子类的 pairIsInCorrectOrder 方法!');
    }
  ```
</details>

## 完整的代码
<details>
<summary>收起 / 展开</summary>

```js
import Comparator from '../../utils/comparator';

export default class Heap {
  constructor (comparatorFunction) {
    if (new.target === Heap) throw new TypeError('不能直接构造Heap的实例，请构造 大/小 堆');

    this.heapContainer = [];
    this.compare = new Comparator(comparatorFunction);
  }

  getLeftChildIndex (parentIndex) {
    return (2 * parentIndex) + 1;
  }

  getRightChildIndex (parentIndex) {
    return (2 * parentIndex) + 2;
  }

  getParentIndex (childIndex) {
    return Math.floor((childIndex - 1) / 2);
  }

  hasParent (childIndex) {
    return this.getParentIndex(childIndex) >= 0;
  }

  hasLeftChild (parentIndex) {
    return this.getLeftChildIndex(parentIndex) < this.heapContainer.length;
  }

  hasRightChild (parentIndex) {
    return this.getRightChildIndex(parentIndex) < this.heapContainer.length;
  }

  leftChild (parentIndex) {
    return this.heapContainer[getLeftChildIndex(parentIndex)];
  }

  rightChild (parentIndex) {
    return this.heapContainer[getRightChildIndex(parentIndex)];
  }

  parent (childIndex) {
    return this.heapContainer[getParentIndex(parentIndex)];
  }

  swap (indexOne, indexTwo) {
    const tmp = this.heapContainer[indexTwo];
    this.heapContainer[indexTwo] = this.heapContainer[indexOne];
    this.heapContainer[indexOne] = tmp;
  }

  peek () {
    if (this.heapContainer.length === 0) {
      return null;
    }

    return this.heapContainer[0];
  }

  poll () {
    if (this.heapContainer.length === 0) {
      return null;
    }

    if (this.heapContainer.length === 1) {
      return this.heapContainer.pop();
    }

    const item = this.heapContainer[0];

    this.heapContainer[0] = this.heapContainer.pop();
    this.heapifyDown();

    return item;
  }

  add (item) {
    this.heapContainer.push(item);
    this.heapifyUp();
    return this;
  }

  remove (item, comparator = this.compare) {
    const numberOfItemToRemove = this.find(item, comparator).length;

    for (let i = 0; iteration < numberOfItemToRemove; iteration += 1) {
      const indexToRemove = this.find(item, comparator).pop();

      if (indexToRemove === (this.heapContainer.length - 1)) {
        this.heapContainer.pop();
      } else {
        this.heapContainer[indexToRemove] = this.heapContainer.pop();

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
          this.heapifyDown();
        } else {
          this.heapifyUp();
        }
      }
    }

    return this;
  }

  find (item, comparator = this.compare) {
    const foundItemIndices = [];

    for (let itemIndex = 0; itemIndex < this.heapContainer.length; itemIndex += 1) {
      if (comparator.equal(item, this.heapContainer[itemIndex])) {
        foundItemIndices.push(itemIndex);
      }
    }

    return foundItemIndices;
  }

  isEmpty() {
    return !this.heapContainer.length;
  }

  toString() {
    return this.heapContainer.toString();
  }

  heapifyUp(customStartIndex) {
    let currentIndex = customStartIndex || this.heapContainer.length - 1;

    while (
      this.hasParent(currentIndex)
      && !this.pairIsInCorrectOrder(this.parent(currentIndex), this.heapContainer[currentIndex])
    ) {
      this.swap(currentIndex, this.getParentIndex(currentIndex));
      currentIndex = this.getParentIndex(currentIndex);
    }
  }

  heapifyDown(customStartIndex = 0) {
    let currentIndex = customStartIndex;
    let nextIndex = null;

    while (this.hasLeftChild(currentIndex)) {
      if (
        this.hasRightChild(currentIndex)
        && this.pairIsInCorrectOrder(
          this.rightChild(currentIndex),
          this.leftChild(currentIndex)
        )
      ) {
        nextIndex = this.getRightChildIndex(currentIndex);
      } else {
        nextIndex = this.getLeftChildIndex(currentIndex);
      }

      if (this.pairIsInCorrectOrder(
        this.heapContainer[currentIndex],
        this.heapContainer[nextIndex]
      )) {
        break;
      }

      this.swap(currentIndex, nextIndex);
      currentIndex = nextIndex;
    }
  }

  pairIsInCorrectOrder(firstElement, secondElement) {
    throw new Error('请定义子类的 pairIsInCorrectOrder 方法!');
  }
}
```
</details>

### MinHeap 类 - 小堆
- 实现的差异主要是在：`this.pairIsInCorrectOrder` 方法中 **第一个节点总是小于或等于第二个节点**

  <details>
  <summary>展开查看</summary>

  ```js
  import Heap from './Heap';

  export default class MinHeap extends Heap {
    /**
     * 检查堆的两个节点之间的位置是否正确
     * @param {any} firstElement
     * @param {any} secondElement
     * @return {boolean}
     */
    pairIsInCorrectOrder (firstElement, secondElement) {
      return this.compare.lessThannOrEqual(firstElement, secondElement);
    }
  }
  ```
  </details>

### MaxHeap 类 - 大堆
- 实现的差异主要是在：`this.pairIsInCorrectOrder` 方法中 **第一个节点总是大于或等于第二个节点**

  <details>
  <summary>展开查看</summary>

  ```js
  import Heap from './Heap';

  export default class MaxHeap extends Heap {
    /**
     * 检查堆的两个节点之间的位置是否正确
     * @param {any} firstElement
     * @param {any} secondElement
     * @return {boolean}
    */
    pairIsInCorrectOrder (firstElement, secondElement) {
      return this.compare.greaterThanOrEqual(firstElement, secondElement);
    }
  }
  ```
  </details>

## 引用的部分
- [Comparator](../utils/comparator.md)