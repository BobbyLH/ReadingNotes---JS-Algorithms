# Priority Queue(优先队列)
在计算机科学中，**优先队列** 是一个和普通的 **队列** 和 **栈** 类似的抽象数据结构。唯一不同的是，它的每个元素都拥有 “优先级” 这个属性 —— 高优先级的元素会比低优先级的元素先被处理，而不是按照元素的入队顺序。而若是两个元素的优先级一致的话，才会按照它们入队的顺序决定它们的优先级。

尽管 **优先队列** 通常是用 **堆** 来实现的，但是它们在本质上有很大的区别。优先队列和 “列表(list)”、“图(map)” 一样都是一个抽象的概念；就像一个列表能够用链表或数组来实现一样，一个优先队列也能够用堆或者其他方式来实现，就比如无序的数组。

## 代码剖析
<details open>
<summary>收起/查看</summary>

### PriorityQueue 类 - 优先队列
- 继承自 [MinHeap](https://github.com/BobbyLH/ReadingNotes---JS-Algorithms/blob/master/data-structures/Heap.md#minheap-%E7%B1%BB---%E5%B0%8F%E5%A0%86)

- 重写了父类的 `this.compare` 方法

- 用 [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) 数据结构来保存优先级的映射关系

  ```js
  class PriorityQueue extends MinHeap {
    constructor () {
      // 调用父类的构造函数
      super();

      // 构造一个 Map，用来保存优先级的映射关系
      // 用Map数据结构储存数据，能确保即便是复杂类型的值，也是唯一的，不会被隐式转换
      this.priority = new Map();

      // 使用自定义的比较方法，兼容带有优先级的比较，而不是使用默认的用 value 的大小来进行比较
      // 即意味着说，父类的 find、pairIsInCorrectOrder 等方法都会受到影响
      // 底层的堆的顺序，是按照优先级而不是值的大小来排序
      this.compare = new Comparator(this.comparePriority.bind(this));
    }
    //……
  }
  ```

#### add - 添加元素
- 重写了父类的 [add](https://github.com/BobbyLH/ReadingNotes---JS-Algorithms/blob/master/data-structures/Heap.md#add---%E6%B7%BB%E5%8A%A0%E8%8A%82%E7%82%B9%E5%88%B0%E5%A0%86%E4%B8%AD) 方法

- 将元素添加到优先队列中，添加的元素会按照指定的优先级(默认为0)进行排序

- 入参：
    - `item` <any> 待新增的元素

    - `priority` <number> 待新增的元素的优先级，默认为 `0`

- 出参(Returns)：<PriorityQueue> 返回优先队列的整个实例

  ```js
    add (item, priority = 0) {
      // 往 Map 里 存储元素和优先级的映射关系
      // key 值为添加的元素
      // value 值为元素的优先级
      this.priority.set(item, priority);
      // 调用父类的 add 方法
      // 即往一个数组中推入该元素，并按照优先级的顺序进行排序，因为在 constructor 里面已经重新了 this.compare
      super.add(item);
      return this;
    }
  ```

#### remove - 删除元素
- 移除优先队列中的某个元素

- 重写了父类的 [remove](https://github.com/BobbyLH/ReadingNotes---JS-Algorithms/blob/master/data-structures/Heap.md#remove---%E6%A0%B9%E6%8D%AE%E4%BC%A0%E5%85%A5%E7%9A%84%E8%8A%82%E7%82%B9%E5%88%A0%E9%99%A4%E5%85%B6%E5%9C%A8%E5%A0%86%E4%B8%AD%E6%89%80%E5%AF%B9%E5%BA%94%E7%9A%84%E5%85%A8%E9%83%A8%E8%8A%82%E7%82%B9) 方法

- 删除元素后会按照优先级重新进行排序

- 入参：
    - `item` <any> 待新增的元素

    - `customFindingComparator` <Comparator> 自定义的比较方法，即可以按照 *优先级* 或 *值* 来移除某个(些)元素

- 出参(Returns)：<PriorityQueue> 返回优先队列的整个实例

  ```js
    remove (item, customFindingComparator) {
      // 调用父类的 remove 方法，移除所有匹配的元素，并重新排序
      super.remove(item, customFindingComparator);
      this.priority.delete(item);
      return this;
    }
  ```

#### changePriority - 改变优先级
- 改变优先队列中某个元素的优先级

- 元素的优先级改变其实是先移除后新增的一个过程

- 入参：
    - `item` <any> 待改变的元素

    - `priority` <number> 元素的优先级，因 `this.add` 中的默认值为 `0`，故而其默认也为 `0`

- 出参(Returns)：<PriorityQueue> 返回优先队列的整个实例

  ```js
    changePriority (item, priority) {
      // 先移除这个元素
      this.remove(item, new Comparator(this.compareValue));
      // 绑定优先级后再次添加到优先队列中
      this.add(item, priority);
      return this;
    }
  ```

#### findByValue - 根据值而不是优先级筛选出元素
- 顾名思义，根据 value 值筛选出所有匹配的元素索引

- 和继承的 find 方法有区别

- 入参：
    - `item` <any> 待改变的元素

- 出参(Returns)：<number[]> 返回所有匹配元素的索引列表

  ```js
    findByValue (item) {
      return this.find(item, new Comparator(this.compareValue));
    }
  ```

#### hasValue - 检查元素是否存在
- 判断的依据是：是否在堆中找到了该元素的索引

- 入参：
    - `item` <any> 待查询的元素

- 出参(Returns)：<boolean> 元素是否存在

  ```js
    hasValue (item) {
      return this.findByValue(item).length > 0;
    }
  ```

#### comparePriority - 根据优先级比较元素
- 数值越大，优先级越高

- 入参：
    - `a` <any> 待比较的元素a

    - `b` <any> 待比较的元素b

- 出参(Returns)：<number>
    - `0` 相等；

    - `1` a大于b；

    - `-1` a小于b。

  ```js
    comparePriority (a, b) {
      if (this.priority.get(a) === this.priority.get(b)) {
        return 0;
      }

      return this.priority.get(a) < this.priority.get(b) ? -1 : 1;
    }
  ```

#### compareValue - 根据元素的值进行比较
- 入参：
    - `a` <any> 待比较的元素a

    - `b` <any> 待比较的元素b

- 出参(Returns)：<number>
    - `0` 相等；

    - `1` a大于b；

    - `-1` a小于b。

  ```js
    compareValue (a, b) {
      if (a === b) return 0;

      return a < b ? -1 : 1;
    }
  ```
</details>

## 完整的代码
<details>
<summary>收起/查看</summary>

```js
import { MinHeap } from './Heap';
import Comparator from '../../utils/comparator';

export default class PriorityQueue extends MinHeap {
  constructor () {
    super();

    this.priority = new Map();
    this.compare = new Comparator(this.comparePriority.bind(this));
  }

  add (item, priority = 0) {
    this.priority.set(item, priority);
    super.add(item);
    return this;
  }

  remove (item, customFindingComparator) {
    super.remove(item, customFindingComparator);
    this.priority.delete(item);
    return this;
  }

  changePriority (item, priority) {
    this.remove(item, new Comparator(this.compareValue));
    this.add(item, priority);
    return this;
  }

  findByValue (item) {
    return this.find(item, new Comparator(this.compareValue));
  }

  hasValue (item) {
    return this.findByValue(item).length > 0;
  }

  comparePriority (a, b) {
    if (this.priority.get(a) === this.priority.get(b)) {
      return 0;
    }

    return this.priority.get(a) < this.priority.get(b) ? -1 : 1;
  }

  compareValue (a, b) {
    if (a === b) return 0;

    return a < b ? -1 : 1;
  }
}
```
</details>

## 引用的部分
- [Comparator](../utils/comparator.md)

- [堆](./Heap.md)