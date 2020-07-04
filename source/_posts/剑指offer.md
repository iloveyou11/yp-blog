---
title: 剑指offer笔记
date: 2019-04-08
categories: 计算机
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---


**1 在一个二维数组中（每个一维数组的长度相同）,每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。**

<!-- more -->

```javascript
function Find(target, array) {
    let rowCount = array.length
    let colCount = array[0].length

    for (let i = rowCount - 1, j = 0; i >= 0 && j < colCount;) {
        if (target === array[i][j]) return true
        if (target > array[i][j]) {
            j++
            continue
        } else {
            i--
            continue
        }
    }
    return false
}
```

**2 请实现一个函数，将一个字符串中的每个空格替换成“%20”。例如，当字符串为We Are Happy,则经过替换之后的字符串为We%20Are%20Happy。**
```javascript
function replaceSpace1(str) {
    let reg = /\s/g
    return str.replace(reg, '%20')
}
```
**用空间换时间**

```javascript
function replaceSpace2(str) {
    let newStr = ''
    for (let i = 0; i < str.length; i++) {
        if (str[i] === ' ') {
            newStr += '%20'
        } else {
            newStr += str[i]
        }
    }
    return newStr
}
```
**节约空间，直接操作原字符串，倒序插入**
```javascript
function replaceSpace3(str) {
    // 先统计空格总数，确定最后一个字符的位置
    let count = 0
    let strArr = str.split('')
    for (let i = 0; i < strArr.length; i++) {
        if (strArr[i] === ' ') count++
    }
    // 注意，要将字符串转化为数组之后才能进行从后往前扩展赋值的操作
    for (let i = strArr.length - 1; i >= 0; i--) {
        if (strArr[i] !== ' ')
            strArr[i + 2 * count] = strArr[i]
        else {
            count--
            strArr[i + 2 * count] = '%'
            strArr[i + 2 * count + 1] = '2'
            strArr[i + 2 * count + 2] = '0'
        }
    }
    return strArr.join('')
}
//console.log(replaceSpace3('hello world'));
```

**3  输入一个链表，按链表值从尾到头的顺序返回一个ArrayList。**

**方法1：使用尾递归**

```javascript
function printListFromTailToHead1(head) {
    let list = []
    let node = head
    if (node != null) {
        if (node.next != null) {
            list = printListFromTailToHead(node.next)
        }
        list.push(node.val)
    }
    return list
}
```
**方法2：使用栈**
```javascript
function printListFromTailToHead2(head) {
    let list = []
    let node = head
    if (node != null) {
        list.unshift(node.val) **先把首节点放进去
        while (node.next != null) {
            node = node.next
            list.unshift(node.val) **依次放入下一个节点值
        }
        return list
    }
}
class Node {
    constructor(val, next) {
        this.val = val;
        this.next = next;
    }
}
let node4 = new Node(4, null)
let node3 = new Node(3, node4)
let node2 = new Node(2, node3)
let node1 = new Node(1, node2)
//console.log(printListFromTailToHead2(node1));
```

**4 输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。 假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。**

 先序遍历特点：第一个值是根节点
 中序遍历特点：根节点左边都是左子树，右边都是右子树
 思路：
 首先根据根节点a将中序遍历划分为两部分，左边为左子树，右边为右子树
 在左子树中根据第一条规则递归，得出左子树
 在右子树中根据第一条规则递归，得出右子树
 最后合成一棵树

```javascript
function reConstructBinaryTree(pre, vin) {
    if (pre.length == 0 || vin.length == 0) {
        return null
    }
    let index = vin.indexOf(pre[0]) ##根节点在vin的索引
    let left = vin.slice(0, index) ##中序左子树
    let right = vin.slice(index + 1) ##中序右子树
    return {
        val: pre[0],
        left: reConstructBinaryTree(pre.slice(1, index + 1), left),
        right: reConstructBinaryTree(pre.slice(index + 1), right)
    }
}
```
**5 用两个栈来实现一个队列，完成队列的Push和Pop操作。 队列中的元素为int类型。**

 分析：
 入队：将元素进栈A
 出队：判断栈B是否为空，如果为空，则将栈A中所有元素pop，并push进栈B，栈B出栈；
 如果不为空，栈B直接出栈。

```javascript
let inStack = [],
    outStack = []

function push(node) {
    inStack.push(node)
}

function pop() {
    if (inStack.length === 0 && outStack.length === 0) return null
    if (!outStack.length) {
        while (inStack.length) {
            outStack.push(inStack.pop())
        }
    }
    return outStack.pop()
}
```

**6 把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个非减排序的数组的一个旋转，输出旋转数组的最小元素。 例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。 NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。**

**方法1：直接找出数组最小值**
```javascript
function minNumberInRotateArray(rotateArray) {
    if (rotateArray.length == 0) {
        return 0
    }
    let min = rotateArray[0]
    for (let i of rotateArray) {
        min = i < min ? i : min
    }
    return min
}
```
**方法2：直接找出开始非递增的值. 非减排序数组的旋转数组，遍历找到第一个小于前一个数的值即为最小值**
```javascript
function minNumberInRotateArray(rotateArray) {
    if (!rotateArray.length) return 0
    for (let i = 1; i < rotateArray.length; i++) {
        if (rotateArray[i] < rotateArray[i - 1])
            return rotateArray[i]
    }
    return rotateArray[0]
}
```
**方法3：二分查找，根据中间值进行判断**
中间元素大于第一个元素，则中间元素位于前面的递增子数组，此时最小元素位于中间元素的后面。我们可以让第一个指针left指向中间元素。
移动之后，第一个指针仍然位于前面的递增数组中。
中间元素小于第一个元素，则中间元素位于后面的递增子数组，此时最小元素位于中间元素的前面。我们可以让第二个指针right指向中间元素。

```javascript
function minNumberInRotateArray(rotateArray) {
    let size = rotateArray.length
    if (size === 0) return 0
        **定义left指针、right指针
    let left = 0,
        right = size - 1
    let mid = 0
        **确保是旋转数组
    while (rotateArray[left] > rotateArray[right]) {
        if (right - left === 1) {
            mid = right
            break
        }
        **这里注意要将小数化为整数，否则mid为小数，导致后面结果均为undefined
        mid = left + Math.floor((right - left) / 2)
            **如果rotateArray[left] rotateArray[right] rotateArray[mid]三者相等
            **无法确定中间元素是属于前面还是后面的递增子数组
            **只能顺序查找
        if (rotateArray[left] == rotateArray[right] && rotateArray[left] == rotateArray[mid]) {
            return _MinOrder(rotateArray, left, right)
        }

        if (rotateArray[mid] > rotateArray[left]) {
            left = mid
        } else {
            right = mid
        }

    }
    return rotateArray[mid]
}

function _MinOrder(array, left, right) {
    let min = array[left]
    for (let i = left; i < right; i++) {
        if (array[i] < min)
            min = array[i]
    }
    return min
}

// let arr = [4, 5, 6, 7, 2, 3]
// console.log(minNumberInRotateArray(arr));
```

**7 大家都知道斐波那契数列，现在要求输入一个整数n，请你输出斐波那契数列的第n项（从0开始，第0项为0）。n<=39**
**方法1：正向相加，以下两种写法是一样的**
```javascript
function Fibonacci(n) {
    if (n <= 0) return 0
    if (n === 1 || n === 2) return 1
    let n1 = 1,
        n2 = 1,
        res = 0
    for (let i = 2; i < n; i++) {
        res = n1 + n2
        n1 = n2
        n2 = res
    }
    return res
}

function Fibonacci(n) {
    if (n === 0) return 0
    if (n === 1) return 1
    let n1 = 0,
        n2 = 1,
        res
    for (let i = 2; i <= n; i++) {
        res = n1 + n2
        n1 = n2
        n2 = res
    }
    return res
}
```
**方法2：使用递归（速度太慢，占用大量内存，不建议使用）**
```javascript
function Fibonacci(n) {
    if (n <= 1) return n
    else return Fibonacci(n - 1) + Fibonacci(n - 1)
}
```
**8 一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法（先后次序不同算不同的结果）。**
 分析：同样是斐波拉契数列，但是与上述的斐波拉契数列不同
 上面的为 0 | 1 1 2 3 5 ……
 本题的为 0 | 1 2 3 5 8 ……
```javascript
function jumpFloor(number) {
    if (number <= 0) return 0
    if (number === 1 || number === 2) return number
    let n1 = 1,
        n2 = 2,
        res
    for (let i = 2; i < number; i++) {
        res = n1 + n2
        n1 = n2
        n2 = res
    }
    return res
}
```
**9 一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。**
 分析：f(n) = f(n-1) + f(n-2) + f(n-3) + ... + f(1) =f(n-1)+f(n-1)=2*f(n-1)
 数列类似于1 2 4 8 16……
```javascript
function jumpFloorII(number) {
    if (number <= 0) return 0
    if (number === 1) return 1
    return 2 * jumpFloorII(number - 1)
}
```
**使用递归**
```javascript
function jumpFloorII(number) {
    if (number <= 0) return 0
    if (number === 1) return 1
    let res = 1
    for (let i = 2; i <= number; i++) {
        res = 2 * res
    }
    return res
}
```
**使用位运算，报错：未通过所有的测试用例，why？**
```javascript
function jumpFloorII(number) {
    let res = 1
    return res << (number - 1)
}
```

**10 输入一个整数，输出该数二进制表示中1的个数。其中负数用补码表示。**
 **普通计算**
 ‘>>>’是无视符号位的右移，>>右移是补符号位，所以负数补1造成死循环
```javascript
function NumberOf1(n) {
    if (n < 0) n = n >>> 0 ##计算补码
    let res = n.toString(2)
    let count = 0
    for (let i = 0; i < res.length; i++) {
        if (res[i] === '1') count++
    }
    return count
}
```
**利用位运算符计算**
```javascript
function NumberOf1(n) {
    let count = 0
    while (n !== 0) {
        count++ **如果一个整数不为0，那么这个整数至少有一位是1
        n = n & (n - 1) ##将二进制的最后一个1变为0
    }
    return count
}
```
**11 给定一个double类型的浮点数base和int类型的整数exponent。求base的exponent次方。**
**传统公式求解时间复杂度O(n)**
```javascript
function Power(base, exponent) {
    let result = 1
    for (let i = 0; i < Math.abs(exponent); i++) {
        result *= base
    }
    if (exponent < 0) result = 1 / result
    return result
}
```
**递归，事件复杂度O(logn)**
```javascript
function Power(base, exponent) {
    let n = Math.abs(exponent)
    if (n === 0 && base !== 0) return 1
    if (n === 1) return base
    let result = Power(base, n >> 1)
    result *= result
    if ((n & 1) === 1) result *= base
    if (exponent < 0) result = 1 / result
    return result
}
```
**综合解法**
```javascript
function Power(base, exponent) {
    if (exponent == 0 && base != 0)
        return 1;
    if (exponent == 1)
        return base;
    if (base == 0 && exponent <= 0) {
        throw new RuntimeException();
    }
    if (base == 0 && exponent > 0) {
        return 0;
    }
    let n = exponent;
    if (exponent < 0) {
        n = -exponent;
    }
    let result = Power(base, n >> 1);
    result *= result;
    if ((n & 1) == 1)
        result *= base;
    if (exponent < 0)
        result = 1 / result;
    return result;
}
```
**12 输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于数组的后半部分。 并保证奇数和奇数，偶数和偶数之间的相对位置不变。**

**方法1：另开空间**
```javascript
function reOrderArray(array) {
    let odds = [],
        evens = []
    array.forEach(item => {
        if (item % 2 === 0) {
            evens.push(item)
        } else {
            odds.push(item)
        }
    })
    return odds.concat(evens)
}

// 采用插入排序，不正确
// function reOrderArray2(array) {
//     for (let i = 1; i < array.length; i++) {
//         if (array[i] % 2 == 1) {
//             for (let j = i; j > 0; j--) {
//                 if (array[j - 1] % 2 == 0) {
//                     let temp = array[j];
//                     array[j] = array[j - 1];
//                     array[j - 1] = temp;
//                 }
//             }
//         }
//     }
// }
```


**13 输入一个链表，输出该链表中倒数第k个结点。**
```javascript
/*function ListNode(x){
    this.val = x;
    this.next = null;
}*/
```
 分析：定义两个指针快指针和慢指针，让快指针先走(k-1)步
 再让快指针和慢指针同时走，快指针走完时，慢指针就到达了倒数第k个节点

```javascript
function FindKthToTail(head, k) {
    if (head == null || k <= 0) return null
    let fastNode = head,
        slowNode = head
        **注意这里是先走k-1步
    for (let i = 1; i < k; i++) {
        if (fastNode.next == null) return null
        fastNode = fastNode.next
    }
    while (fastNode.next != null) {
        fastNode = fastNode.next
        slowNode = slowNode.next
    }
    return slowNode
}
```
**14 输入一个链表，反转链表后，输出新链表的表头。**
```javascript
function ReverseList(pHead) {
    if (pHead == null) {
        return null
    }
    let pre = null,
        next = null
    while (head != null) {
        //做循环，如果当前节点不为空的话，始终执行此循环，此循环的目的就是让当前节点从指向next到指向pre
        //如此就可以做到反转链表的效果
        //先用next保存head的下一个节点的信息，保证单链表不会因为失去head节点的原next节点而就此断裂
        next = head.next;
        //保存完next，就可以让head从指向next变成指向pre了，代码如下
        head.next = pre;
        //head指向pre后，就继续依次反转下一个节点
        //让pre，head，next依次向后移动一个节点，继续下一次的指针反转

        pre = head;
        head = next;
    }
    return pre
}
```
**15 输入两个单调递增的链表，输出两个链表合成后的链表，当然我们需要合成后的链表满足单调不减规则。**
**方法1：采用递归版本**

```javascript
function Merge(pHead1, pHead2) {
    if (pHead1 == null) return pHead2
    if (pHead2 == null) return pHead1
    if (pHead1.val < pHead2.val) {
        pHead1.next = Merge(pHead1.next, pHead2)
        return pHead1
    } else {
        pHead2.next = Merge(pHead1, pHead2.next)
        return pHead2
    }
}
```
**方法2：采用非递归版本**
 比较两个链表的首结点，哪个小的的结点则合并到第三个链表尾结点，并向前移动一个结点。
 步骤一结果会有一个链表先遍历结束，或者没有
 第三个链表尾结点指向剩余未遍历结束的链表
 返回第三个链表首结点
```javascript
function Merge(pHead1, pHead2) {
    if (!pHead1) return pHead2
    if (!pHead2) return pHead1

    let head
    if (pHead1.val <= pHead2.val) {
        head = pHead1
        pHead1 = pHead1.next
    } else {
        head = pHead2
        pHead2 = pHead2.next
    }
    let p = head
    while (pHead1 && pHead2) {
        if (pHead1.val <= pHead2.val) {
            p.next = pHead1
            pHead1 = pHead1.next
            p = p.next
        } else {
            p.next = pHead2
            pHead2 = pHead2.next
            p = p.next
        }
    }
    if (!pHead1) p.next = pHead2
    if (!pHead2) p.next = pHead1
    return head
}
```
**16 输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）**
```javascript
/* function TreeNode(x) {
    this.val = x;
    this.left = null;
    this.right = null;
} */
```
**方法1**
```javascript
function HasSubtree(pRoot1, pRoot2) {
    let flag = false
        //当Tree1和Tree2都不为零的时候，才进行比较。否则直接返回false
    if (pRoot1 != null && pRoot2 != null) {
        if (pRoot1.val === pRoot2.val) {
            //以这个根节点为为起点判断是否包含Tree2
            flag = _hasSubtree(pRoot1, pRoot2)
        }
        //如果找不到，那么就再去root的左儿子当作起点，去判断时候包含Tree2
        if (!flag) {
            flag = HasSubtree(pRoot1.left, pRoot2)
        }
        //如果还找不到，那么就再去root的右儿子当作起点，去判断时候包含Tree2
        if (!flag) {
            flag = HasSubtree(pRoot1.right, pRoot2)
        }
    }
    return flag
}
```
判断以node1为根的子树与以node2为根的子树是否完全相等
```javascript
function _hasSubtree(node1, node2) {
    //如果Tree2已经遍历完了都能对应的上，返回true
    if (node2 == null) {
        return true;
    }
    //如果Tree2还没有遍历完，Tree1却遍历完了。返回false
    if (node1 == null) {
        return false;
    }
    //如果其中有一个点没有对应上，返回false
    if (node1.val != node2.val) {
        return false;
    }
    //如果根节点对应的上，那么就分别去子节点里面匹配
    return _hasSubtree(node1.left, node2.left) && _hasSubtree(node1.right, node2.right);
}
```
**方法2：使用短路版本进行改写，方法同上，只是简化了代码，但是测试不通过？**

```javascript
function HasSubtree(node1, node2) {
    if (node1 == null || node2 == null) return false;
    return _hasSubtree(node1, node2) ||
        HasSubtree(node1.left, node2) ||
        HasSubtree(node1.right, node2);
}

function _hasSubtree(pRoot1, pRoot2) {
    if (pRoot2 == null) return true
    if (pRoot1 == null) return false
    if (pRoot1.val === pRoot2.val) {
        return _hasSubtree(pRoot1.left, pRoot2.left) && _hasSubtree(pRoot1.right, pRoot2.right)
    } else {
        return false
    }
}
```
**17 操作给定的二叉树，将其变换为源二叉树的镜像。**
**方法1:采用递归**
```javascript
function Mirror(root) {
    if (!root) return null
    let temp = root.left
    root.left = root.right
    root.right = temp
    if (root.left) { Mirror(root.left) }
    if (root.right) { Mirror(root.right) }
    return root
}
```

**18 输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字**
```javascript
//尚未想到思路
function printMatrix(matrix) {
    // write code here
}
```
**19 定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））。**
 **分析：**
 思路：利用一个辅助栈来存放最小值
​     栈  3，4，2，5，1
​     辅助栈 3，3，2，2，1
 每入栈一次，就与辅助栈顶比较大小，如果小就入栈，如果大就入栈当前的辅助栈顶
 当出栈时，辅助栈也要出栈
 这种做法可以保证辅助栈顶一定都当前栈的最小值

```javascript
let stack1 = [], //主栈
    stack2 = [] //辅助栈

function push(value) {
    stack1.push(value)
    if (!stack2) {
        stack2.push(value)
    } else if (value <= stack2.top()) {
        stack2.push(value)
    }
}

function pop() {
    if (stack2.top() === stack1.top()) {
        stack2.pop()
    }
    stack1.pop()
}

function top() {
    return stack1[stack1.length - 1]
}

function min() {
    return stack2[stack2.length - 1]
}
```
**19 输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。**
 **分析：**
 入栈1,2,3,4,5
 出栈4,5,3,2,1
 借用一个辅助的栈，遍历压栈顺序，先讲第一个放入栈中，这里是1，
 然后判断栈顶元素是不是出栈顺序的第一个元素，这里是4，很显然1≠4，
 所以我们继续压栈，直到相等以后开始出栈，出栈一个元素，则将出栈顺序向后移动一位，直到不相等，这样循环等压栈顺序遍历完成，
 如果辅助栈还不为空，说明弹出序列不是该栈的弹出顺序。

 测试未通过,why？
```javascript
function IsPopOrder1(pushV, popV) {
    if ((!pushV) || (!popV)) return false
    let stack = []
    let index = 0
    for (let i = 0; i < pushV.length; i++) {
        stack.push(pushV[i])
            //如果栈不为空，且栈顶元素等于弹出序列
        while (stack.length !== 0 && stack[stack.length - 1] === popV[index]) {
            stack.pop()
            index++
        }
    }
    return stack.length === 0
}

// 和以上解法一样，j相当于index
function IsPopOrder2(pushV, popV) {
    let list = []
    for (let i = 0, j = 0; i < pushV.length; i++) {
        list.push(pushV[i]);
        while (list.length !== 0 && list[list.length - 1] == popV[j]) {
            list.pop();
            ++j;
        }
    }
    return list.length === 0
}
```
**20 从上往下打印出二叉树的每个节点，同层节点从左至右打印。（层次遍历，借助队列实现）**
```javascript
/* function TreeNode(x) {
    this.val = x;
    this.left = null;
    this.right = null;
} */
function PrintFromTopToBottom(root) {
    let nodes = [],
        vals = []
    if (!root) return vals
    nodes.push(root)
    while (nodes.length) {
        let cur = nodes.shift()
        vals.push(cur.val)
        if (cur.left) {
            nodes.push(cur.left)
        }
        if (cur.right) {
            nodes.push(cur.right)
        }
    }
    return vals
}
```

**21 输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则输出Yes,否则输出No。假设输入的数组的任意两个数字都互不相同。**

**思路：**
已知条件：后序序列最后一个值为root；二叉搜索树左子树值都比root小，右子树值都比root大。
1、确定root；
2、遍历序列（除去root结点），找到第一个大于root的位置，则该位置左边为左子树，右边为右子树；
3、遍历右子树，若发现有小于root的值，则直接返回false；
4、分别判断左子树和右子树是否仍是二叉搜索树（即递归步骤1、2、3）。

```javascript
function VerifySquenceOfBST(sequence) {
    let left = [],
        right = []
    let root
    if (sequence.length === 0) return false
    let index //左右子树界限
    root = sequence[sequence.length - 1]
    let i = 0
    for (; i < sequence.length - 1; i++) {
        if (sequence[i] > root) break
    }
    for (let j = i; j < sequence.length - 1; ++j) {
        if (sequence[j] < root) return false
    }
    if (i !== 0) {
        for (let m = 0; m < i; m++)
            left.push(sequence[m])
    }
    if (i !== sequence.length - 2) {
        for (let m = 0; m < i; m++)
            right.push(sequence[m])
    }
    let leftFlag = true,
        rightFlag = true
    if (left.length > 1) leftFlag = VerifySquenceOfBST(left)
    if (right.length > 1) VerifySquenceOfBST(right)

    return (leftFlag && rightFlag)
}
```
**方法2：**

**思路：找住二叉查找树的特点：左子树<根<=右子树  使用分治思想**

```javascript
function VerifySquenceOfBST(sequence) {
    if (sequence.length === 0) return false
    if (sequence.length === 1) return true
    return _judge(sequence, 0, sequence.length - 1)
}

function _judge(a, start, end) {
    if (start >= end) return true
    let i = start
    while (a[i] < a[end]) {
        ++i
    }
    for (let j = i; j < end; j++) {
        if (a[j] < a[end])
            return false
    }
    return _judge(a, start, i - 1) && _judge(a, i, end - 1)
}
```
**22 输入一颗二叉树的跟节点和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。(注意: 在返回值的list中，数组长度大的数组靠前)**
```javascript
/* function TreeNode(x) {
    this.va = x;
    this.left = null;
    this.right = null;
} */
//可以使用递归解决，但是测试不通过
function FindPath(root, expectNumber) {
    let listAll = [],
        list = []
    if (root == null) return listAll
    list.push(root.val)
    expectNumber -= root.val
    if (expectNumber == 0 && root.left == null && root.right == null) {
        listAll.push(list)
    }
    FindPath(root.left, expectNumber)
    FindPath(root.right, expectNumber)
    list.pop()
    return listAll
}
```
**23 输入一个复杂链表（每个节点中有节点值，以及两个指针，一个指向下一个节点，另一个特殊指针指向任意一个节点），返回结果为复制后复杂链表的head。**

（注意，输出结果中请不要返回参数中的节点引用，否则判题程序会直接返回空）

```javascript
/*function RandomListNode(x){
    this.label = x;
    this.next = null;
    this.random = null;
}*/
//方法1:递归
//此题转化为一个头结点和除去头结点剩余部分，剩余部分操作和原问题一致
function Clone(pHead) {
    if (pHead == null) return null

    //使用new一个对象无法测试通过
    let newNode = new RandomListNode(pHead.x)
    let newNode = pHead

    newNode.next = pHead.next
    newNode.random = pHead.random

    //开始递归
    newNode.next = Clone(pHead.next)
    return newNode
}
```
**方法2：三步走——无法测试通过**

```javascript
function Clone(pHead) {
    if (pHead == null) {
        return null;
    }

    let currentNode = pHead;
    //1、复制每个结点，如复制结点A得到A1，将结点A1插到结点A后面；
    while (currentNode != null) {
        let cloneNode = currentNode;
        let nextNode = currentNode.next;
        currentNode.next = cloneNode;
        cloneNode.next = nextNode;
        currentNode = nextNode;
    }

    currentNode = pHead;
    //2、重新遍历链表，复制老结点的随机指针给新结点，如A1.random = A.random.next;
    while (currentNode != null) {
        currentNode.next.random = currentNode.random == null ? null : currentNode.random.next;
        currentNode = currentNode.next.next;
    }

    //3、拆分链表，将链表拆分为原链表和复制后的链表
    currentNode = pHead;
    let pCloneHead = pHead.next;
    while (currentNode != null) {
        let cloneNode = currentNode.next;
        currentNode.next = cloneNode.next;
        cloneNode.next = cloneNode.next == null ? null : cloneNode.next.next;
        currentNode = currentNode.next;
    }

    return pCloneHead;
}
```
**24 输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。**

**方法1：非递归版**
解题思路：
1.核心是中序遍历的非递归算法。
2.修改当前遍历节点与前一遍历节点的指针指向。

```javascript
function Convert(root) {
    if (root == null)
        return null;
    let stack = []
    let p = root;
    let pre = null; 用于保存中序遍历序列的上一节点
    let isFirst = true;
    while (p != null || stack.length !== 0) {
        while (p != null) {
            stack.push(p);
            p = p.left;
        }
        p = stack.pop();
        if (isFirst) {
            root = p; 将中序遍历序列中的第一个节点记为root
            pre = root;
            isFirst = false;
        } else {
            pre.right = p;
            p.left = pre;
            pre = p;
        }
        p = p.right;
    }
    return root;
}
```

**方法二：递归版**
解题思路：
1.将左子树构造成双链表，并返回链表头节点。
2.定位至左子树双链表最后一个节点。
3.如果左子树链表不为空的话，将当前root追加到左子树链表。
4.将右子树构造成双链表，并返回链表头节点。
5.如果右子树链表不为空的话，将该链表追加到root节点之后。
6.根据左子树链表是否为空确定返回的节点。

```javascript
function Convert(root) {
    if (root == null)
        return null;
    if (root.left == null && root.right == null)
        return root;
    1.将左子树构造成双链表，并返回链表头节点
    let left = Convert(root.left);
    let p = left;
    2.定位至左子树双链表最后一个节点
    while (p != null && p.right != null) {
        p = p.right;
    }
    3.如果左子树链表不为空的话，将当前root追加到左子树链表
    if (left != null) {
        p.right = root;
        root.left = p;
    }
    4.将右子树构造成双链表，并返回链表头节点
    let right = Convert(root.right);
    5.如果右子树链表不为空的话，将该链表追加到root节点之后
    if (right != null) {
        right.left = root;
        root.right = right;
    }
    return left != null ? left : root;
}
```
**方法三：改进递归版**
解题思路：
思路与方法二中的递归版一致，仅对第2点中的定位作了修改，
新增一个全局变量记录左子树的最后一个节点。

```javascript
function Convert(root) {
    if (root == null)
        return null;
    if (root.left == null && root.right == null) {
        leftLast = root; 最后的一个节点可能为最右侧的叶节点
        return root;
    }
    1.将左子树构造成双链表，并返回链表头节点
    let left = Convert(root.left);
    3.如果左子树链表不为空的话，将当前root追加到左子树链表
    if (left != null) {
        leftLast.right = root;
        root.left = leftLast;
    }
    leftLast = root; 当根节点只含左子树时，则该根节点为最后一个节点
    4.将右子树构造成双链表，并返回链表头节点
    let right = Convert(root.right);
    5.如果右子树链表不为空的话，将该链表追加到root节点之后
    if (right != null) {
        right.left = root;
        root.right = right;
    }
    return left != null ? left : root;
}
```
**25 输入一个字符串,按字典序打印出该字符串中字符的所有排列。例如输入字符串abc,则打印出由字符a,b,c所能排列出来的所有字符串abc,acb,bac,bca,cab和cba。**

```javascript
let result = []

function Permutation(str) {
    if (str !== '') dfs(str, 0)
    return result
}

function _dfs(str, s) {
    let len = str.length
    if (s === len) {
        result.push(str)
        return
    }
    for (let i = s; i < len; ++i) {
        if (i != s && str[s] == str[i]) continue;
        let temp = str[s]
        str[s] = str[i]
        str[i] = str[s]
            [str[s], str[i]] = [str[i], str[s]]
        dfs(str, s + 1)
    }
}
```
**26 数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0。**

**方法1：快速排序**
先对这个数组进行排序，在已排序的数组中，位于中间位置的数字就是超过数组长度一半的那个数。

```javascript
function _quickSort(arr) {
    if (arr.length <= 1) return arr
    let pivotIndex = Math.floor(arr.length / 2)
    let pivot = arr.splice(pivotIndex, 1)[0]
    let left = [],
        right = []
    for (let i = 0; i < arr.length; i++) {
        arr[i] <= pivot ? left.push(arr[i]) : right.push(arr[i])
    }
    return _quickSort(left).concat([pivot], _quickSort(right))
}

function MoreThanHalfNum_Solution1(numbers) {
    let len = numbers.length
    let sortedNumbers = _quickSort(numbers)
    let midNum = sortedNumbers[Math.floor(len / 2)]
    let count = 0
    for (let i = 0; i < len; i++) {
        if (sortedNumbers[i] === midNum) count++
    }
    if (count * 2 > len) return midNum
    return 0
}
```
**方法2：直接使用键值对存储数和出现的次数**

```javascript
function MoreThanHalfNum_Solution2(numbers) {
    let len = numbers.length
    let json = {}
    for (let i = 0; i < len; i++) {
        let key = numbers[i]
        if (json.hasOwnProperty(key)) {
            json[key]++
        } else {
            json[key] = 1
        }
    }
    for (let key in json) {
        if (json[key] * 2 > len) return key
    }
    return 0
}
```
**方法3：依次循环，直接统计出出现最多的数字**

```javascript
function MoreThanHalfNum_Solution3(numbers) {
    let len = numbers.length
    if (len === 0) return 0
    let num = numbers[0],
        count = 1
    for (let i = 1; i < len; i++) {
        numbers[i] == num ? count++ : count--
            if (count == 0) {
                num = numbers[i]
                count = 1
            }
    }
    count = 0
    for (let i = 0; i < len; i++) {
        if (numbers[i] == num) {
            count++
        }
    }
    if (count * 2 > len) return num
    return 0
}
```

**26 输入n个整数，找出其中最小的K个数。**

**1)快排（针对找第k大的数，更快捷一些）**

```javascript
function GetLeastNumbers_Solution1(input, k) {
    let sorted = _quickSort(input)
    return sorted.slice(0, k)
}
```
**2）冒泡排序(测试不正确)**

```javascript
function GetLeastNumbers_Solution2(input, k) {
    let len = input.length
    for (let i = 0; i < k; i++) {
        for (let j = i + 1; j < len; j++) {
            if (input[i + 1] < input[i]) {
                let temp = input[i + 1]
                input[i + 1] = input[i]
                input[i] = temp
            }
        }
    }
    return input.slice(0, k)
}
```
**27 计算连续子向量的最大和（包括正数和负数）**

分析：使用动态规划
F（i）：以array[i]为末尾元素的子数组的和的最大值，子数组的元素的相对位置不变
F（i）=max（F（i-1）+array[i],array[i]）

```javascript
function FindGreatestSumOfSubArray(array) {
    let res = array[0]
    let max = array[0]
    for (let i = 0; i < array.length; i++) {
        max = Math.max(max + array[i], array[i])
        res = Math.max(max, res)
    }
    return res
}

function FindGreatestSumOfSubArray(array) {
    if (!array) return 0
    let total = array[0],
        maxSum = array[0]
    for (let i = 1; i < array.length; i++) {
        total >= 0 ? total += array[i] : total = array[0]
        if (total > maxSum) maxSum = total
    }
    return maxSum
}
```
**28 求出任意非负整数区间中1出现的次数（从1 到 n 中1出现的次数）。**

最直接的方法统计

```javascript
function NumberOf1Between1AndN_Solution(n) {
    let count = 0,
        charLen = 0
    for (let i = 0; i <= n; i++) {
        let str = i.toString()
        charLen = str.length
        for (let j = 0; j < charLen; j++) {
            if (str[j] === '1')
                count++
        }
    }
    return count
}
//运行超时:您的程序未能在规定时间内运行结束，请检查是否循环有错或算法复杂度过大。
function NumberOf1Between1AndN_Solution(n) {
    let count = 0
    for (let i = 0; i <= n; i++) {
        let temp = i
        while (i) {
            if (temp % 10 === 1) count++
                temp /= 10
        }
    }
    return count
}
//这种解法？
function NumberOf1Between1AndN_Solution(n) {
    let ret = 0,
        base = 1;
    while (n / base) {
        let bit = (n / base) - (n / base) / 10 * 10;
        if (bit == 0)
            ret += n / (base * 10) * base;
        if (bit == 1)
            ret += n / (base * 10) * base + (n - n / base * base) + 1;
        if (bit > 1)
            ret += (n / (base * 10) + 1) * base;
        base *= 10;
    }
    return ret;
}
```
**29 输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个.所以在这里自定义一个比较大小的函数，比较两个字符串s1, s2大小的时候，先将它们拼接起来，比较s1+s2,和s2+s1那个大，如果s1+s2大，那说明s2应该放前面，所以按这个规则，s2就应该排在s1前面。** 

```javascript
function PrintMinNumber(numbers) {
    if (!numbers) return ""
    let len = numbers.length
    let newArr = numbers.sort(_compare)
    return newArr.join('')
}
//实现自定义的排序函数
function _compare(n1, n2) {
    let number1 = `${n1}${n2}`
    let number2 = `${n2}${n1}`
    if (number1 > number2) return 1
    else if (number1 < number2) return -1
    else return 0
}
```
**30 把只包含质因子2、3和5的数称作丑数（Ugly Number）。例如6、8都是丑数，但14不是，因为它包含质因子7。 习惯上我们把1当做是第一个丑数。求按从小到大的顺序的第N个丑数。**

**思路解析：**
一个丑数一定由另一个丑数乘以2或者乘以3或者乘以5得到，那么我们从1开始乘以2,3,5，就得到2,3,5三个丑数，在从这三个丑数出发乘以2,3,5就得到4，6,10,6，9,15,10,15,25九个丑数，我们发现这种方法会得到重复的丑数，而且我们题目要求第N个丑数，这样的方法得到的丑数也是无序的。那么我们可以维护三个队列：
（1）丑数数组： 1
乘以2的队列：2
乘以3的队列：3
乘以5的队列：5
选择三个队列头最小的数2加入丑数数组，同时将该最小的数乘以2,3,5放入三个队列；
（2）丑数数组：1,2
乘以2的队列：4
乘以3的队列：3，6
乘以5的队列：5，10
选择三个队列头最小的数3加入丑数数组，同时将该最小的数乘以2,3,5放入三个队列；
（3）丑数数组：1,2,3
乘以2的队列：4,6
乘以3的队列：6,9
乘以5的队列：5,10,15
……

```javascript
function GetUglyNumber_Solution(index) {
    if (index < 7) return index
    let res = []
    res[0] = 1
    let t2 = 0,
        t3 = 0,
        t5 = 0
    for (let i = 1; i < index; i++) {
        res[i] = Math.min(res[t2] * 2, Math.min(res[t3] * 3, res[t5] * 5));
        if (res[i] == res[t2] * 2) t2++;
        if (res[i] == res[t3] * 3) t3++;
        if (res[i] == res[t5] * 5) t5++;
    }
    return res[index - 1]
}
```
**31 在一个字符串(0<=字符串长度<=10000，全部由字母组成)中找到第一个只出现一次的字符,并返回它的位置, 如果没有则返回 -1（需要区分大小写）.**

**暴力搜索**

```javascript
function FirstNotRepeatingChar1(str) {
    if (!str) return -1
    let count = {}
    for (let i = 0; i < str.length; i++) {
        if (!count.hasOwnProperty(str[i])) {
            count[str[i]] = 1
        } else {
            count[str[i]]++
        }
    }
    for (let i in str) {
        if (count[str[i]] === 1) {
            return i
        }
    }
}

//存在漏洞
function FirstNotRepeatingChar(str) {
    if (!str) return -1
    for (let i = 0; i < str.length; i++) {
        if (str.lastIndexOf(str[i]) === i) {
            console.log(`只在${i}位出现了${str[i]}`);
            return str[i]
        }
    }
    return -1
}
```
**32 在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组,求出这个数组中的逆序对的总数P。**

**思路：参考《剑指offer》用归并排序的思想， 时间复杂度O(nlogn)**
function InversePairs(data) {
​    write code here
}

**33 输入两个链表，找出它们的第一个公共结点。**

/*function ListNode(x){
​    this.val = x;
​    this.next = null;
}*/
**思路：用两个指针扫描”两个链表“，最终两个指针到达 null 或者到达公共结点。**

```javascript
function FindFirstCommonNode(pHead1, pHead2) {
    let p1 = pHead1,
        p2 = pHead2
    while (p1 !== p2) {
        p1 = (p1 == null ? pHead2 : p1.next)
        p2 = (p2 == null ? pHead1 : p2.next)
    }
    return p1
}
//思路与上述相同
function FindFirstCommonNode(pHead1, pHead2) {
    let p1 = pHead1;
    let p2 = pHead2;
    while (p1 !== p2) {
        if (p1 != null) p1 = p1.next;
        if (p2 != null) p2 = p2.next;
        if (p1 != p2) {
            if (p1 == null) p1 = pHead2;
            if (p2 == null) p2 = pHead1;
        }
    }
    return p1;
}
```
**34 统计一个数字在排序数组中出现的次数。**

**思路：由于数组有序，所以使用二分查找方法定位k的第一次出现位置和最后一次出现位置**

```javascript
function GetNumberOfK(data, k) {
    let firstIndex = _getFirst(data, k)
    let lastIndex = _getLast(data, k)
    return lastIndex - firstIndex + 1
}
//获取k在data数组中第一次出现的index
function _getFirst(data, k) {
    let start = 0,
        end = data.length - 1
    let mid = Math.floor(start, end)
    while (start <= end) {
        if (data[mid] < k) {
            start = mid + 1
        } else {
            end = mid - 1
        }
        mid = Math.floor(start, end)
    }
    return start
}
//获取k在data数组中最后一次出现的index
function _getLast(data, k) {
    let start = 0,
        end = data.length - 1
    let mid = Math.floor(start, end)
    while (start <= end) {
        if (data[mid] <= k) {
            start = mid + 1
        } else {
            end = mid - 1
        }
        mid = Math.floor(start, end)
    }
    return end
}
```
**35 输入一棵二叉树，求该树的深度。**

**从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。

/* function TreeNode(x) {
​    this.val = x;
​    this.left = null;
​    this.right = null;
} */
**方法1：采用递归实现**

```javascript
function TreeDepth(pRoot) {
    if (!pRoot) return 0
    return Math.max(TreeDepth(pRoot.left) + 1, TreeDepth(pRoot.right) + 1)
}
```
**方法2：非递归——层次遍历(测试未通过)**

```javascript
function TreeDepth(pRoot) {
    if (pRoot == null) {
        return 0;
    }
    let queue = []
    queue.push(pRoot);
    let depth = 0,
        count = 0,
        nextCount = 1;
    while (queue.length != 0) {
        let top = queue.pop();
        count++;
        if (top.left != null) {
            queue.push(top.left);
        }
        if (top.right != null) {
            queue.push(top.right);
        }
        if (count == nextCount) {
            nextCount = queue.length;
            count = 0;
            depth++;
        }
    }
    return depth;
}
//测试同样未通过
function TreeDepth(pRoot) {
    if (pRoot) return 0
    let queue = []
    queue.push(pRoot)
    let level = 0
    while (queue) {
        let len = queue.length
        level++
        while (len--) {
            let temp = queue[0]
            queue.pop()
            if (temp.left) queue.push(temp.left)
            if (temp.right) queue.push(temp.right)
        }
    }
    return level
}
```
**36 输入一棵二叉树，判断该二叉树是否是平衡二叉树。**

**方法1：利用剪枝**
改为从下往上遍历，如果子树是平衡二叉树，则返回子树的高度；如果发现子树不是平衡二叉树，
则直接停止遍历，这样至多只对每个结点访问一次。

测试未通过，请检查是否存在语法错误或者数组越界非法访问等情况
```javascript
function IsBalanced_Solution(root) {
    return _getDepth(root) != -1
}

function _getDepth(root) {
    if (root == null) return 0
    let left = _getDepth(root.left)
    if (left == -1) return -1
    let right = _getDepth(root.right)
    if (right == -1) return -1
    return Math.abs(left - right) > 1 ? -1 : 1 + Math.max(left, right)
}
```
**方法2：**
测试仍然未通过，请检查是否存在语法错误或者数组越界非法访问等情况

```javascript
let isBalanced = true

function IsBalanced_Solution(root) {
    _getDepth(root);
    return isBalanced;
}

function _getDepth(root) {
    if (root == null)
        return 0;
    let left = getDepth(root.left);
    let right = getDepth(root.right);

    if (Math.abs(left - right) > 1) {
        isBalanced = false;
    }
    return right > left ? right + 1 : left + 1;
}
```
**37 一个整型数组里除了两个数字之外，其他的数字都出现了两次。请写程序找出这两个只出现一次的数字。**

首先：位运算中异或的性质：两个相同数字异或=0，一个数和0异或还是它本身。
当只有一个数出现一次时，我们把数组中所有的数，依次异或运算，最后剩下的就是落单的数，因为成对儿出现的都抵消了。
依照这个思路，我们来看两个数（我们假设是AB）出现一次的数组。我们首先还是先异或，剩下的数字肯定是A、B异或的结果，这个结果的二进制中的1，表现的是A和B的不同的位。
我们就取第一个1所在的位数，假设是第3位，接着把原数组分成两组，分组标准是第3位是否为1。
如此，相同的数肯定在一个组，因为相同数字所有位都相同，而不同的数，肯定不在一组。
然后把这两个组按照最开始的思路，依次异或，剩余的两个结果就是这两个只出现一次的数字。

```javascript
function FindNumsAppearOnce(array) {
    let num1, num2
    if (array == null || array.length < 2) return
    let temp = 0
    for (let i = 0; i < array.length; i++) {
        temp ^= array[i]
    }

    let indexOf1 = _findFirstBit(temp)

    for (let i = 0; i < array.length; i++) {
        if (_isBit(array[i], indexOf1))
            num1 ^= array[i]
        else
            num2 ^= array[i]
    }
    return [num1, num2]

}
//找到第一个为1的二进制位
function _findFirstBit(num) {
    let indexBit = 0;
    while (((num & 1) == 0) && (indexBit) < 8 * 4) {
        num = num >> 1;
        ++indexBit;
    }
    return indexBit;
}
//判断这个数第index位是否为1
function _isBit(num, indexBit) {
    num = num >> indexBit;
    return (num & 1) == 1;
}
```
**38 有多少种连续的正数序列的和为sum(至少包括两个数)**

```javascript
function FindContinuousSequence(sum) {
    let result = []
    let low = 1,
        high = 2
        求和公式是(a0+an)*n/2
    while (high > low) {
        let cur = (high + low) * (high - low + 1) / 2
            //相等，那么就将窗口范围的所有数添加进结果集
        if (cur == sum) {
            let list = []
            for (let i = low; i <= high; i++)
                list.push(i)
            result.push(list)
            low++
            //如果当前窗口内的值之和小于sum，那么右边窗口右移一下
        } else if (cur < sum) {
            high++
            //如果当前窗口内的值之和大于sum，那么左边窗口右移一下
        } else {
            low++
        }
    }
    return result
}
```
**39 输入一个递增排序的数组和一个数字S，在数组中查找两个数，使得他们的和正好是S，如果有多对数字的和等于S，输出两个数的乘积最小的。**

```javascript
function FindNumbersWithSum(array, sum) {
    let res = []
    let len = array.length
    let i = 0,
        j = len - 1
    while (i < j) {
        找到的第一组（相差最大的）就是乘积最小的。
        可以这样证明：考虑x+y=C（C是常数），x*y的大小。
        不妨设y>=x，y-x=d>=0，即y=x+d, 2x+d=C, x=(C-d)/2, x*y=x(x+d)=(C-d)(C+d)/4=(C^2-d^2)/4，
        也就是x*y是一个关于变量d的二次函数，对称轴是y轴，开口向下。
        d是>=0的，d越大, x*y也就越小。
        if (array[i] + array[j] === sum) {
            res.push(array[i])
            res.push(array[j])
            break
        }
        while (i < j && array[i] + array[j] > sum) --j;
        while (i < j && array[i] + array[j] < sum) ++i;
    }
    return res
}
```

**40 对于一个给定的字符序列S，请你把其循环左移K位后的序列输出**

提交未通过

```javascript
function LeftRotateString1(str, n) {
    let len = str.length
    if (len == 0 || n == 0) return str;
    n = n % len
    str += str
    return str.slice(n, n + len)
}
//提交未通过
function LeftRotateString2(str, n) {
    let len = str.length
    if (len == 0 || n == 0) return str;
    let s1 = str.slice(0, n)
    let s2 = str.slice(n, len)
    return s2 + s1
}

//本地测试通过，但是提交不通过
function LeftRotateString3(str, n) {
    let len = str.length
    if (len <= 0 || n <= 0) return str
    n = n % len
    let s1 = _reverse(str.slice(0, n), 0, n - 1)
    let s2 = _reverse(str.slice(n, len), n, len - 1)
    let res = _reverse(s1 + s2, 0, len - 1)
    return res
}

function _reverse(str, start, end) {
    let arr = str.split('')
    while (start < end) {
        let temp = arr[start]
        arr[start] = arr[end]
        arr[end] = temp
        start++, end--
    }
    return arr.join('')
}
```

**41 单词顺序反转**

利用反转函数

```javascript
function ReverseSentence1(str) {
    let arr = str.split(' ')
    arr.reverse()
    return arr.join(' ')
}
```
利用新数组存储反转单词
```javascript
function ReverseSentence2(str) {
    if (str.trim() == "") return str
    let arr = str.split(' ')
    let temp = []
    for (let i = arr.length - 1; i >= 0; i--) {
        temp.push(arr[i])
    }
    return temp.join(' ')
}
```
先反转整个句子，再依次反转每个单词
```javascript
function ReverseSentence3(str) {
    let len = str.length
    let arr = str.split('')
        判断句子中是否含有' '字符，如果没有，则为一个单词，不做处理直接返回即可
    if (arr.includes(' ')) {
        _reverse(arr, 0, len - 1)
        let blank = -1
        for (let i = 0; i < len; i++) {
            if (arr[i] == ' ') {
                let nextBlank = i
                _reverse(arr, blank + 1, nextBlank - 1);
                blank = nextBlank;
            }
        }
        _reverse(arr, blank + 1, len - 1) //最后一个单词单独进行反转
    }
    return arr.join('')
}

function _reverse(arr, start, end) {
    while (start < end) {
        let temp = arr[start]
        arr[start] = arr[end]
        arr[end] = temp
        start++, end--
    }
}
```
**42 有2个大王,2个小王(一副牌原本是54张)...**

随机从中抽出了5张牌，大\ 小 王可以看成任何数字, 并且A看作1, J为11, Q为12, K为13
求抽到顺子的概率
为了方便起见,你可以认为大小王是0。

**思路：**
满足条件：
1、max-min<5
2、除0外没有重复的数字
3、数组长度为5

```javascript
function IsContinuous(numbers) {
    let map = {}
    let len = numbers.length,
        max = -1,
        min = 14
    if (len < 5) return false
    for (let i = 0; i < len; i++) {
        if (!map[numbers[i]]) {
            map[numbers[i]] = 1
        } else {
            map[numbers[i]]++;
        }
        if (numbers[i] == 0) continue

        if (map[numbers[i]] > 1) return false
        if (numbers[i] > max) max = numbers[i]
        if (numbers[i] < min) min = numbers[i]
    }
    if (max - min < 5) {
        return true
    }
    return false;
}
```

**43 随机指定一个数m,让编号为0的小朋友开始报数，小朋友的编号是从0到n-1.每次喊到m-1的那个小朋友要出列唱首歌并出列，再从下一个小朋友开始问最后留下的一个小朋友是谁？**

问题描述：n个人（编号0~(n-1))，从0开始报数，报到(m-1)的退出，剩下的人 继续从0开始报数。求胜利者的编号。

**思路1：数学归纳法——不懂...**
令f[i]表示i个人玩游戏报m退出最后胜利者的编号，最后的结果自然是f[n]。
递推公式
f[1]=0;
f[i]=(f[i-1]+m)%i;  (i>1)

```javascript
function LastRemaining_Solution(n, m) {
    if (n == 0)
        return -1;
    if (n == 1)
        return 0;
    else
        return (LastRemaining_Solution(n - 1, m) + m) % n;
}
```
**思路2：用数组模拟环——不懂...**

```javascript
function LastRemaining_Solution(n, m) {
    if (n < 1 || m < 1) return -1
    let array = []
    let i = -1,
        step = 0,
        count = n
    while (count > 0) {
        i++; //指向上一个被删除对象的下一个元素。
        if (i >= n) i = 0; //模拟环。
        if (array[i] == -1) continue; //跳过被删除的对象。
        step++; //记录已走过的。
        if (step == m) { //找到待删除的对象。
            array[i] = -1;
            step = 0;
            count--;
        }
    }
    return i; //返回跳出循环时的i,即最后一个被设置为-1的元素
}
```
**44 求1+2+3+...+n，要求不能使用乘除法、for、while、if、else、switch、case等关键字及条件判断语句（A?B:C）。**

共同点：一，利用利用短路 && 来实现 if的功能；二，利用递归来实现循环while的功能
不同点：方法一：递归实现1+2+..+n;方法二：n(n+1)/2,递归实现n(n+1)；方法三，利用Math实现n(n+1)
**思路1：递归实现1+2+..+n**

```javascript
function Sum_Solution(n) {
    let result = n
    let temp = n && Sum_Solution(n - 1)
    result += temp
    return result
}
```
**思路2： n(n+1)/2,递归实现n(n+1)；**
**思路3，利用Math实现n(n+1)**

```javascript
function Sum_Solution(n) {
    return (Math.pow(n, 2) + n) >> 2
}
```
**45 写一个函数，求两个整数之和，要求在函数体内不得使用+、-、*、/四则运算符号。**

首先看十进制是如何做的： 5+7=12，三步走
第一步：相加各位的值，不算进位，得到2。
第二步：计算进位值，得到10. 如果这一步的进位值为0，那么第一步得到的值就是最终结果。
第三步：重复上述两步，只是相加的值变成上述两步的得到的结果2和10，得到12。

同样我们可以用三步走的方式计算二进制值相加： 5-101，7-111 第一步：相加各位的值，不算进位，得到010，二进制每位相加就相当于各位做异或操作，101^111。
第二步：计算进位值，得到1010，相当于各位做与操作得到101，再向左移一位得到1010，(101&111)<<1。
第三步重复上述两步， 各位相加 010^1010=1000，进位值为100=(010&1010)<<1。
继续重复上述两步：1000^100 = 1100，进位值为0，跳出循环，1100为最终结果。
```javascript
function Add(num1, num2) {
    while (num2 != 0) {
        let temp = num1 ^ num2;
        num2 = (num1 & num2) << 1;
        num1 = temp;
    }
    return num1;
}
```
**46 将一个字符串转换成一个整数(实现Integer.valueOf(string)的功能，但是string不符合数字要求时返回0)，要求不能使用字符串转换整数的库函数。 数值为0或者字符串不是一个合法的数值则返回0。**

以下算法测试不通过，如输入+123,输出为0
```javascript
function StrToInt(str) {
    if (str.trim() == '') return 0
    let symbol = 1
        判断正负号
    if (str[0] == '-') {
        symbol = -1
        str[0] = '0'
    } else if (str[0] == '+') {
        symbol = 1
        str[0] = '0'
    }
    let sum = 0
    for (let i = 0; i < str.length; i++) {
        if (str[i] < '0' || str[i] > '9') {
            sum = 0
            break
        }
        sum = sum * 10 + parseInt(str[i])
    }
    return symbol * sum
}

//测试通过！
function StrToInt2(str) {
    const numlist = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', '+', '-']
    let sum = 0,
        symbol = 1
    if (str == '') return 0
    for (let char of str) {
        if (numlist.includes(char)) {
            if (char == '+') {
                symbol = 1
                continue
            } else if (char == '-') {
                symbol = -1
                continue
            } else {
                sum = sum * 10 + numlist.indexOf(char)
            }
        } else {
            sum = 0
            break
        }
    }
    return sum * symbol
}
```
**47 在一个长度为n的数组里的所有数字都在0到n-1的范围内。 数组中某些数字是重复的，但不知道有几个数字是重复的。也不知道每个数字重复几次。请找出数组中任意一个重复的数字。**

**思路1:**
最简单的方法：我最直接的想法就是构造一个容量为N的辅助数组B，原数组A中每个数对应B中下标，首次命中，B中对应元素+1。如果某次命中时，B中对应的不为0，
说明，前边已经有一样数字了，那它就是重复的了。

时间复杂度O（n），空间复杂度O（n），算法优点是简单快速，比用set更轻量更快，不打乱原数组顺序。
```javascript
function duplicate(numbers, duplication) {
    //这里要特别注意~找到任意重复的一个值并赋值到duplication[0]
    //函数返回True/False
    let count = []
    for (let i = 0; i < numbers.length; i++) {
        if (count[numbers[i]] == undefined) {
            count[numbers[i]] = 1
        } else {
            duplication[0] = numbers[i];
            return true;
        }
    }
    return false
}
```
时间O(n)， 空间O(1)_这个看不太懂...
```javascript
function duplicate(numbers, duplication) {
    if (!numbers) return false
    for (let i = 0; i < numbers.length; i++) {
        while (numbers[i] !== i) {
            if (numbers[i] == numbers[numbers[i]]) {
                duplication[0] = numbers[i];
                return true;
            }
            let temp = numbers[i];
            numbers[i] = numbers[temp];
            numbers[temp] = temp;
        }
    }
    return false
}
```
**48 给定一个数组A[0,1,...,n-1],请构建一个数组B[0,1,...,n-1],其中B中的元素B[i]=A[0]*A[1]*...*A[i-1]*A[i+1]*...*A[n-1]。不能使用除法。**

参考https://www.nowcoder.com/questionTerminal/94a4d381a68b47b7a8bed86f2975db46
```javascript
function multiply(array) {
    write code here
}
console.log(StrToInt2('123'))
```
