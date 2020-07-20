# Priority Queue(优先队列)
在计算机科学中，**优先队列** 是一个和普通的 **队列** 和 **栈** 类似的抽象数据结构。唯一不同的是，它的每个元素都拥有 “优先级” 这个属性 —— 高优先级的元素会比低优先级的元素先被处理，而不是按照元素的入队顺序。而若是两个元素的优先级一致的话，才会按照它们入队的顺序决定它们的优先级。

尽管 **优先队列** 通常是用 **堆** 来实现的，但是它们在本质上有很大的区别。优先队列和 “列表(list)”、“图(map)” 一样都是一个抽象的概念；就像一个列表能够用链表或数组来实现一样，一个优先队列也能够用堆或者其他方式来实现，就比如无序的数组。

## 代码剖析

[Comparator 剖析](../utils/comparator.md)

[堆](./Heap.md)

### 优先队列
```js
import { MinHeap } from './Heap';
import Comparator from '../../utils/comparator';

export default class PriorityQueue extends MinHeap {
  constructor () {
    // 调用父类的构造函数
    super();

    // 构造一个 Map，用来保存优先级的映射关系
    this.priority = new Map();

    // 使用自定义的比较方法，兼容带有优先级的比较，而不是使用默认的用 value 的大小来进行比较
    this.compare = new Comparator(this.comparePriority.bind(this));
  }

  /**
   * 优先级比较的方法
   * @param {any} a
   * @param {any} b
   * @return {number}
   */
  comparePriority (a, b) {
    if (this.priority.get(a) === this.priority.get(b)) {
      return 0;
    }

    return this.priority.get(a) < this.priority.get(b) ? -1 : 1;
  }
}
```

