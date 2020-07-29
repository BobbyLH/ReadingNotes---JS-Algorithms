# Priority Queue(优先队列)
在计算机科学中，**优先队列** 是一个和普通的 **队列** 和 **栈** 类似的抽象数据结构。唯一不同的是，它的每个元素都拥有 “优先级” 这个属性 —— 高优先级的元素会比低优先级的元素先被处理，而不是按照元素的入队顺序。而若是两个元素的优先级一致的话，才会按照它们入队的顺序决定它们的优先级。

尽管 **优先队列** 通常是用 **堆** 来实现的，但是它们在本质上有很大的区别。优先队列和 “列表(list)”、“图(map)” 一样都是一个抽象的概念；就像一个列表能够用链表或数组来实现一样，一个优先队列也能够用堆或者其他方式来实现，就比如无序的数组。

## 代码剖析

### 优先队列
[Comparator 剖析](../utils/comparator.md)

[堆](./Heap.md)


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
    // 即意味着说，父类的 find、pairIsInCorrectOrder 等方法都会受到影响
    // 底层的堆的顺序，是按照优先级而不是值的大小来排序
    this.compare = new Comparator(this.comparePriority.bind(this));
  }

  /**
   * 重写了 add 方法
   * 将元素添加到优先队列中
   * @param {any} item
   * @param {number} priority
   * @return {PriorityQueue}
   */
  add (item, priority = 0) {
    // 用Map数据结构储存数据，能确保即便是复杂类型的值，也是唯一的，不会被隐式转换
    // key 值为添加的元素
    // value 值为元素的优先级
    this.priority.set(item, priority);
    // 调用父类的 add 方法
    // 即往一个数组中推入该元素，并按照优先级的顺序进行排序，因为在 constructor 里面已经重新了 this.compare
    // 详见 Heap: https://github.com/BobbyLH/ReadingNotes---JS-Algorithms/blob/master/data-structures/Heap.md
    super.add(item);
    return this;
  }

  /**
   * 重写了 remove 方法
   * 移除优先队列中的某个元素
   * @param {any} item
   * @param {Comparator} customFindingComparator
   * @return {PriorityQueue}
   */
  remove (item, customFindingComparator) {
    // 调用父类的 remove 方法，移除所有匹配的元素，并重新排序
    super.remove(item, customFindingComparator);
    this.priority.delete(item);
    return this;
  }

  /**
   * 改变优先队列中某个元素的优先级
   * @param {any} item
   * @param {number} priority
   * @return {PriorityQueue}
   */
  changePriority (item, priority) {
    // 先移除这个元素
    this.remove(item, new Comparator(this.compareValue));
    // 绑定优先级后再次添加到优先队列中
    this.add(item, priority);
    return this;
  }


  /**
   * 和继承的 find 方法区别
   * 根据 value 值筛选出所有匹配的元素索引
   * @param {any} item
   * @return {number[]}
   */
  findByValue (item) {
    // 返回所有匹配元素的索引列表
    return this.find(item, new Comparator(this.compareValue));
  }

  /**
   * 检查元素是否存在
   * @param {any} item
   * @return {boolean}
   */
  hasValue (item) {
    // 判断的依据是：是否在堆中找到了该元素的索引
    return this.findByValue(item).length > 0;
  }

  /**
   * 根据优先级比较元素
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

  /**
   * 根据元素的值进行比较
   * @param {any} a
   * @param {any} b
   * @return {number}
   */
  compareValue (a, b) {
    if (a === b) return 0;

    return a < b ? -1 : 1;
  }
}
```

