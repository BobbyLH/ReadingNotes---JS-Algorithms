# 哈希表(Hash-Table)
**哈希表(Hash-Table)** 是一种关联数组的抽象数据类型，具体表现则是 key 和 value 的映射关系。一个哈希表使用一个 *哈希函数(hash function)* 来从一堆的储存桶或插槽数组中，计算出想要查找的值的索。

在理想状态下，哈希函数会给每个桶都分配一个唯一的 key，但是大多数时候哈希表都使用的是一个不足够完善的哈希函数，它可能会导致 *哈希冲突(hash collisions)* —— 可能会为多个 key 生成相同的索引，而若是发生了这种情况就必须有一套兼容的机制。

## 代码剖析

[链表](./Linked-List.md)

```js
import LinkedList from '../LinkedList';

// 哈希表的大小会直接影响哈希冲突发生的概率大小
// 哈希表越大，哈希冲突发生的概率越小
// 32 在这里只是一个纯展示作用，以此来观察如何解决哈希冲突
const defaultHashTableSize = 32;

export default class HashTable {
  /**
   * @param {number} hashTableSize // 哈希表大小
   */
  constructor (hashTableSize = defaultHashTableSize) {
    // 根据 hashTableSize 创建一个填充满空的链表的 buckets
    this.buckets = Array(hashTableSize).fill(null).map(() => new LinkedList());

    // 用对象来保存键和其哈希数字的映射，以此达到快速查询的目的
    this.keys = {};
  }

  /**
   * 将 字符串 key 转化为 哈希数字
   * @param {string} key
   * @return {number}
   */
  hash (key) {
    // 为了简单起见，这里遍历 key 的全部字符，来计算出 hash 值
    // 也可以用 多项式(ploynomial) 字符哈希 来降低哈希冲突的概率：
    // hash = charCodeAt(0) * PRIME^(n-1) + charCodeAt(1) * PRIME^(n-2) + ... + charCodeAt(n-1)
    // 👆 charCodeAt(i) 是 key 的第 i 个字符，n 是 key 的长度，PRIME 是一个任意的素数，比如 31
    const hash = Array.from(key).reduce(
      (hashAccumulator, keySymbol) => (hashAccumulator + keySymbol.charCodeAt(0)),
      0
    );

    // 取余，以符合 哈希表的大小
    return hash % this.buckets.length;
  }


  /**
   * 根据 key 在哈希表的某个链表中，设置其某个节点的 value 值
   * @param {string} key
   * @param {any} value
   */
  set (key, value) {
    // 根据 key 计算出 哈希数字
    const keyHash = this.hash(key);
    // 将 key 和它的 哈希值 保存为键值对
    this.keys[key] = keyHash;

    // 根据哈希值确定某个链表
    const bucketLinkedList = this.buckets[keyHash];
    // 调用链表的 find 方法，找出 key 值相同的节点
    const node = bucketLinkedList.find({
      callback: nodeValue => nodeValue.key === key
    });

    if (!node) {
      // 没有节点，则添加一个节点
      // 并将节点的 value 值保存为一个拥有 key 和 value 属性的对象
      bucketLinkedList.append({ key, value });
    } else {
      // 将节点的 value 属性中的 value 值更新成 传入的 value 值
      node.value.value = value;
    }
  }

  /**
   * 根据 key 删除其保存在哈希表中，某个链表的节点
   * @param {string} key
   * @return {LinkedListNode | null}
   */
  delete (key) {
    // 根据 key 计算出 哈希数字
    const keyHash = this.hash(key);
    // 根据哈希值确定某个链表
    const bucketLinkedList = this.buckets[keyHash];
    // 调用链表的 find 方法，找出 key 值相同的节点
    const node = bucketLinkedList.find({
      callback: nodeValue => nodeValue.key === key
    });

    if (node) {
      return bucketLinkedList.delete(node.value);
    }

    return null;
  }

  /**
   * 根据 key 获取保存在哈希表中，某个链表节点的 value 值
   * @param {string} key
   * @return {any | undefined}
   */
  get (key) {
    // 根据哈希值确定某个链表
    const bucketLinkedList = this.buckets[this.hash(key)];

    // 调用链表的 find 方法，找出 key 值相同的节点
    const node = bucketLinkedList.find({
      callback: nodeValue => nodeValue.key === key
    });

    return node ? node.value.value : undefined;
  }

  /**
   * 某个 key 是否已经保存在哈希表中
   * @param {string} key
   * @return {boolean}
   */
  has (key) {
    return Object.hasOwnProperty.call(this.keys, key);
  }

  /**
   * 返回哈希表中已经储存的键
   * @return {string[]}
   */
  getKeys () {
    return Object.keys(this.keys);
  }
}
```