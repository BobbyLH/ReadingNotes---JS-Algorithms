# 值的比较 —— Comparator

```js
class Comparator {
  constructor (compareFunction) {
    // this.compare 为比较方法，可以初始化时传入，否则使用默认的比较方法
    this.compare = compareFunction || Comparator.defaultCompareFunction;
  }

  /**
   * Comparator 默认进行比较的静态方法，参数应该是字符串或者数字
   * @param {(string|number)} a
   * @param {(string|number)} b
   * @returns {number}
   */
  static defaultCompareFunction (a, b) {
    if (a === b) return 0;

    return a < b ? -1 : 1;
  }

  /**
   * 检验 "a" 和 "b" 是否相等
   * @param {any} a
   * @param {any} b
   * @return {boolean}
   */
  equal(a, b) {
    return this.compare(a, b) === 0;
  }

  /**
   * 检验 "a" 是否比 "b" 小
   * @param {any} a
   * @param {any} b
   * @return {boolean}
   */
  lessThan(a, b) {
    return this.compare(a, b) < 0;
  }

  /**
   * 检验 "a" 是否比 "b" 大
   * @param {any} a
   * @param {any} b
   * @return {boolean}
   */
  greaterThan(a, b) {
    return this.compare(a, b) > 0;
  }

  /**
   * 检验 "a" 是否大于等于 "b"
   * @param {any} a
   * @param {any} b
   * @return {boolean}
   */
  greaterThanOrEqual(a, b) {
    return this.greaterThan(a, b) || this.equal(a, b);
  }

  /**
   * 颠倒 this.compare 最终入参的顺序，即将不相等的比较结果翻转
   */
  reverse() {
    const compareOriginal = this.compare;
    this.compare = (a, b) => compareOriginal(b, a);
  }
}
```