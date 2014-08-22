# JDK

## ArrayDeque
```
private boolean delete(int i) {
    checkInvariants();
    final E[] elements = this.elements;
    final int mask = elements.length - 1;   // length一定为2的幂
    final int h = head;
    final int t = tail;
    final int front = (i - h) & mask;
    final int back  = (t - i) & mask;
    // Invariant: head <= i < tail mod circularity
    if (front >= ((t - h) & mask))
        throw new ConcurrentModificationException();
    // Optimize for least element motion
    if (front < back) { // 比较前后两段哪个长
        if (h <= i) {
            System.arraycopy(elements, h, elements, h + 1, front);
        } else { // Wrap around
            System.arraycopy(elements, 0, elements, 1, i);
            elements[0] = elements[mask];
            System.arraycopy(elements, h, elements, h + 1, mask - h);
        }
        elements[h] = null;
        head = (h + 1) & mask;
        return false;
    } else {
        if (i < t) { // Copy the null tail as well
            System.arraycopy(elements, i + 1, elements, i, back);
            tail = t - 1;
        } else { // Wrap around
            System.arraycopy(elements, i + 1, elements, i, mask - i);
            elements[mask] = elements[0];
            System.arraycopy(elements, 1, elements, 0, t);
            tail = (t - 1) & mask;
        }
        return true;
    }
}
```

```
private void allocateElements(int numElements) {
    int initialCapacity = MIN_INITIAL_CAPACITY;
    // Find the best power of two to hold elements.
    // Tests "<=" because arrays aren't kept full.
    if (numElements >= initialCapacity) {
        initialCapacity = numElements;
        initialCapacity |= (initialCapacity >>>  1);
        initialCapacity |= (initialCapacity >>>  2);
        initialCapacity |= (initialCapacity >>>  4);
        initialCapacity |= (initialCapacity >>>  8);
        initialCapacity |= (initialCapacity >>> 16);
        initialCapacity++;
        if (initialCapacity < 0)   // Too many elements, must back off
            initialCapacity >>>= 1;// Good luck allocating 2 ^ 30 elements
    }
    elements = (E[]) new Object[initialCapacity];
}
```

```
private void doubleCapacity() {
    assert head == tail;
    int p = head;
    int n = elements.length;
    int r = n - p; // number of elements to the right of p
    int newCapacity = n << 1;
    if (newCapacity < 0)
        throw new IllegalStateException("Sorry, deque too big");
    Object[] a = new Object[newCapacity];
    System.arraycopy(elements, p, a, 0, r); // shallow copy
    System.arraycopy(elements, 0, a, r, p);
    elements = (E[])a;
    head = 0;
    tail = n;
}
```

## HashMap
This map usually acts as a binned (bucketed) hash table, but when bins get too large, they are transformed into bins of TreeNodes
```

```

## LinkedHashMap
```

```

## Arrays
```
private static int binarySearch0(long[] a, int fromIndex, int toIndex, long key) {
    int low = fromIndex;
    int high = toIndex - 1;

    while (low <= high) {
        int mid = (low + high) >>> 1;   // 逻辑右移解决了溢出
        long midVal = a[mid];

        if (midVal < key)
            low = mid + 1;
        else if (midVal > key)
            high = mid - 1;
        else
            return mid; // key found
    }
    return -(low + 1);  // key not found. 总能得到后一个结果
}
```

## BigInteger


# Guava

## math.IntMath
```
  // x < y返回1, 否则0
  static int lessThanBranchFree(int x, int y) {
    // The double negation is optimized away by normal Java, but is necessary for GWT
    // to make sure bit twiddling works as expected.
    return ~~(x - y) >>> (Integer.SIZE - 1);
  }
```

```
  // 注意HALF_EVEN的实现
  public static int log2(int x, RoundingMode mode) {
    checkPositive("x", x);
    switch (mode) {
      case UNNECESSARY:
        checkRoundingUnnecessary(isPowerOfTwo(x));
        // fall through
      case DOWN:
      case FLOOR:
        return (Integer.SIZE - 1) - Integer.numberOfLeadingZeros(x);

      case UP:
      case CEILING:
        return Integer.SIZE - Integer.numberOfLeadingZeros(x - 1);

      case HALF_DOWN:
      case HALF_UP:
      case HALF_EVEN:
        // Since sqrt(2) is irrational, log2(x) - logFloor cannot be exactly 0.5
        int leadingZeros = Integer.numberOfLeadingZeros(x);
        // 判断小数部分是否超过0.5
        int cmp = MAX_POWER_OF_SQRT2_UNSIGNED >>> leadingZeros;
        int logFloor = (Integer.SIZE - 1) - leadingZeros;
        return logFloor + lessThanBranchFree(cmp, x);

      default:
        throw new AssertionError();
    }
  }
  
  // 最大的32位floor(2^(n+0.5))
  @VisibleForTesting static final int MAX_POWER_OF_SQRT2_UNSIGNED = 0xB504F333;
```

```
  // 各种利用缓存
  public static int log10(int x, RoundingMode mode) {
    checkPositive("x", x);
    int logFloor = log10Floor(x);
    int floorPow = powersOf10[logFloor];
    switch (mode) {
      case UNNECESSARY:
        checkRoundingUnnecessary(x == floorPow);
        // fall through
      case FLOOR:
      case DOWN:
        return logFloor;
      case CEILING:
      case UP:
        return logFloor + lessThanBranchFree(floorPow, x);
      case HALF_DOWN:
      case HALF_UP:
      case HALF_EVEN:
        // sqrt(10) is irrational, so log10(x) - logFloor is never exactly 0.5
        return logFloor + lessThanBranchFree(halfPowersOf10[logFloor], x);
      default:
        throw new AssertionError();
    }
  }

  private static int log10Floor(int x) {
    /*
     * Based on Hacker's Delight Fig. 11-5, the two-table-lookup, branch-free implementation.
     *
     * The key idea is that based on the number of leading zeros (equivalently, floor(log2(x))),
     * we can narrow the possible floor(log10(x)) values to two.  For example, if floor(log2(x))
     * is 6, then 64 <= x < 128, so floor(log10(x)) is either 1 or 2.
     */
    int y = maxLog10ForLeadingZeros[Integer.numberOfLeadingZeros(x)];
    /*
     * y is the higher of the two possible values of floor(log10(x)). If x < 10^y, then we want the
     * lower of the two possible values, or y - 1, otherwise, we want y.
     */
    return y - lessThanBranchFree(x, powersOf10[y]);
  }

  // maxLog10ForLeadingZeros[i] == floor(log10(2^(Integer.SIZE - i)))
  @VisibleForTesting static final byte[] maxLog10ForLeadingZeros = {9, 9, 9, 8, 8, 8,
    7, 7, 7, 6, 6, 6, 6, 5, 5, 5, 4, 4, 4, 3, 3, 3, 3, 2, 2, 2, 1, 1, 1, 0, 0, 0, 0};

  @VisibleForTesting static final int[] powersOf10 = {1, 10, 100, 1000, 10000,
    100000, 1000000, 10000000, 100000000, 1000000000};

  // halfPowersOf10[i] = largest int less than 10^(i + 0.5)
  @VisibleForTesting static final int[] halfPowersOf10 =
      {3, 31, 316, 3162, 31622, 316227, 3162277, 31622776, 316227766, Integer.MAX_VALUE};
```

```
  // 几种特殊情况
  public static int pow(int b, int k) {
    checkNonNegative("exponent", k);
    switch (b) {
      case 0:
        return (k == 0) ? 1 : 0;
      case 1:
        return 1;
      case (-1):
        return ((k & 1) == 0) ? 1 : -1;
      case 2:
        return (k < Integer.SIZE) ? (1 << k) : 0;
      case (-2):
        if (k < Integer.SIZE) {
          return ((k & 1) == 0) ? (1 << k) : -(1 << k); // 注意负2的整数幂的处理
        } else {
          return 0;
        }
      default:
        // continue below to handle the general case
    }
    for (int accum = 1;; k >>= 1) {
      switch (k) {
        case 0:
          return accum;
        case 1:
          return b * accum;
        default:
          accum *= ((k & 1) == 0) ? 1 : b;
          b *= b;
      }
    }
  }
```

```
  public static int divide(int p, int q, RoundingMode mode) {
    checkNotNull(mode);
    if (q == 0) {
      throw new ArithmeticException("/ by zero"); // for GWT
    }
    int div = p / q;
    int rem = p - q * div; // equal to p % q

    if (rem == 0) {
      return div;
    }

    /*
     * Normal Java division rounds towards 0, consistently with RoundingMode.DOWN. We just have to
     * deal with the cases where rounding towards 0 is wrong, which typically depends on the sign of
     * p / q.
     *
     * signum is 1 if p and q are both nonnegative or both negative, and -1 otherwise.
     */
    int signum = 1 | ((p ^ q) >> (Integer.SIZE - 1));
    boolean increment;
    switch (mode) {
      case UNNECESSARY:
        checkRoundingUnnecessary(rem == 0);
        // fall through
      case DOWN:
        increment = false;
        break;
      case UP:
        increment = true;
        break;
      case CEILING:
        increment = signum > 0;
        break;
      case FLOOR:
        increment = signum < 0;
        break;
      case HALF_EVEN:
      case HALF_DOWN:
      case HALF_UP:
        int absRem = abs(rem);
        int cmpRemToHalfDivisor = absRem - (abs(q) - absRem);
        // subtracting two nonnegative ints can't overflow
        // cmpRemToHalfDivisor has the same sign as compare(abs(rem), abs(q) / 2).
        if (cmpRemToHalfDivisor == 0) { // exactly on the half mark
          increment = (mode == HALF_UP || (mode == HALF_EVEN & (div & 1) != 0));
        } else {
          increment = cmpRemToHalfDivisor > 0; // closer to the UP value
        }
        break;
      default:
        throw new AssertionError();
    }
    return increment ? div + signum : div;
  }
```

```
  // 有偶数则除2，两个奇数则gcd((a-b)/2, min(a, b))
  public static int gcd(int a, int b) {
    /*
     * The reason we require both arguments to be >= 0 is because otherwise, what do you return on
     * gcd(0, Integer.MIN_VALUE)? BigInteger.gcd would return positive 2^31, but positive 2^31
     * isn't an int.
     */
    checkNonNegative("a", a);
    checkNonNegative("b", b);
    if (a == 0) {
      // 0 % b == 0, so b divides a, but the converse doesn't hold.
      // BigInteger.gcd is consistent with this decision.
      return b;
    } else if (b == 0) {
      return a; // similar logic
    }
    /*
     * Uses the binary GCD algorithm; see http://en.wikipedia.org/wiki/Binary_GCD_algorithm.
     * This is >40% faster than the Euclidean algorithm in benchmarks.
     */
    int aTwos = Integer.numberOfTrailingZeros(a);
    a >>= aTwos; // divide out all 2s
    int bTwos = Integer.numberOfTrailingZeros(b);
    b >>= bTwos; // divide out all 2s
    while (a != b) { // both a, b are odd
      // The key to the binary GCD algorithm is as follows:
      // Both a and b are odd.  Assume a > b; then gcd(a - b, b) = gcd(a, b).
      // But in gcd(a - b, b), a - b is even and b is odd, so we can divide out powers of two.

      // We bend over backwards to avoid branching, adapting a technique from
      // http://graphics.stanford.edu/~seander/bithacks.html#IntegerMinOrMax

      int delta = a - b; // can't overflow, since a and b are nonnegative

      int minDeltaOrZero = delta & (delta >> (Integer.SIZE - 1));
      // equivalent to Math.min(delta, 0)

      a = delta - minDeltaOrZero - minDeltaOrZero; // sets a to Math.abs(a - b)
      // a is now nonnegative and even

      b += minDeltaOrZero; // sets b to min(old a, b)
      a >>= Integer.numberOfTrailingZeros(a); // divide out all 2s, since 2 doesn't divide b
    }
    return a << min(aTwos, bTwos);
  }
```

```
  // 判断是否溢出
  public static int checkedAdd(int a, int b) {
    long result = (long) a + b;
    // 一个专门的MathPreconditions负责抛出不同类型不同参数的异常
    checkNoOverflow(result == (int) result);
    return (int) result;
  }
```

```
  // 判断溢出的pow
  /**
   * Returns the {@code b} to the {@code k}th power, provided it does not overflow.
   *
   * <p>{@link #pow} may be faster, but does not check for overflow.
   *
   * @throws ArithmeticException if {@code b} to the {@code k}th power overflows in signed
   *         {@code int} arithmetic
   */
  public static int checkedPow(int b, int k) {
    checkNonNegative("exponent", k);
    switch (b) {
      case 0:
        return (k == 0) ? 1 : 0;
      case 1:
        return 1;
      case (-1):
        return ((k & 1) == 0) ? 1 : -1;
      case 2:
        checkNoOverflow(k < Integer.SIZE - 1);
        return 1 << k;
      case (-2):
        checkNoOverflow(k < Integer.SIZE);
        return ((k & 1) == 0) ? 1 << k : -1 << k;
      default:
        // continue below to handle the general case
    }
    int accum = 1;
    while (true) {
      switch (k) {
        case 0:
          return accum;
        case 1:
          return checkedMultiply(accum, b);
        default:
          if ((k & 1) != 0) {
            accum = checkedMultiply(accum, b);
          }
          k >>= 1;
          if (k > 0) {
            checkNoOverflow(-FLOOR_SQRT_MAX_INT <= b & b <= FLOOR_SQRT_MAX_INT);
            b *= b;
          }
      }
    }
  }

  @VisibleForTesting static final int FLOOR_SQRT_MAX_INT = 46340;
```

```
  // 编译时搞定
  public static int factorial(int n) {
    checkNonNegative("n", n);
    return (n < factorials.length) ? factorials[n] : Integer.MAX_VALUE;
  }

  private static final int[] factorials = {
      1,
      1,
      1 * 2,
      1 * 2 * 3,
      1 * 2 * 3 * 4,
      1 * 2 * 3 * 4 * 5,
      1 * 2 * 3 * 4 * 5 * 6,
      1 * 2 * 3 * 4 * 5 * 6 * 7,
      1 * 2 * 3 * 4 * 5 * 6 * 7 * 8,
      1 * 2 * 3 * 4 * 5 * 6 * 7 * 8 * 9,
      1 * 2 * 3 * 4 * 5 * 6 * 7 * 8 * 9 * 10,
      1 * 2 * 3 * 4 * 5 * 6 * 7 * 8 * 9 * 10 * 11,
      1 * 2 * 3 * 4 * 5 * 6 * 7 * 8 * 9 * 10 * 11 * 12};
```

```
  public static int binomial(int n, int k) {
    checkNonNegative("n", n);
    checkNonNegative("k", k);
    checkArgument(k <= n, "k (%s) > n (%s)", k, n);
    if (k > (n >> 1)) {
      k = n - k;
    }
    if (k >= biggestBinomials.length || n > biggestBinomials[k]) {
      return Integer.MAX_VALUE;
    }
    switch (k) {
      case 0:
        return 1;
      case 1:
        return n;
      default:
        long result = 1;
        for (int i = 0; i < k; i++) {
          result *= n - i;
          result /= i + 1;
        }
        return (int) result;
    }
  }

  // binomial(biggestBinomials[k], k) fits in an int, but not binomial(biggestBinomials[k]+1,k).
  @VisibleForTesting static int[] biggestBinomials = {
    Integer.MAX_VALUE,
    Integer.MAX_VALUE,
    65536,
    2345,
    477,
    193,
    110,
    75,
    58,
    49,
    43,
    39,
    37,
    35,
    34,
    34,
    33
  };
```

```
  /**
   * Returns the arithmetic mean of {@code x} and {@code y}, rounded towards
   * negative infinity. This method is overflow resilient.
   *
   * @since 14.0
   */
  public static int mean(int x, int y) {
    // Efficient method for computing the arithmetic mean.
    // The alternative (x + y) / 2 fails for large values.
    // The alternative (x + y) >>> 1 fails for negative values.
    return (x & y) + ((x ^ y) >> 1);
  }
```