# KMP算法

## 基础知识

- KMP算法能解决的问题：文本串（父串）里面是否出现模式串（字串）
  
  - 暴力方法解决：模式串和文本串挨个匹配，在匹配完整个模式串之前发生了不匹配，那么模式串整体向后移动一位，再从开始位置与文本串挨个匹配，依次类推。
  
  - 



为什么要使用最长相等前后缀？

需要充分利用已经匹配过的字符串，考虑找到与不匹配的字符前面的字符串的后缀相等的前缀，且保证后缀和前缀的长度最大。

思路：因为不匹配的字符前面的字符串是已经匹配上了的，所以借助找与该字符串的后缀及其长度相等的前缀，借此来跳过已经匹配上了的字符串的前缀部分，从前缀后面的一位字符开始重新匹配。
