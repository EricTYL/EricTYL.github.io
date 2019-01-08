---
layout: post
title:  "LeetCode 654 Maximum Binary Tree"
tags: [Ruby, LeetCode, Data Structure and Algorithms, 技術札記]
show_excerpts: false
---

給一個元素為獨立正整數的陣列，找出最大值當樹的根，然後用一樣的方法，拿最大值左右兩邊的陣列分別建立左子樹和右子樹。

[654. Maximum Binary Tree](https://leetcode.com/problems/maximum-binary-tree/description/)

本題使用遞迴（recursion）的方式來解，實作如下：

```ruby
# Definition for a binary tree node.
# class TreeNode
#     attr_accessor :val, :left, :right
#     def initialize(val)
#         @val = val
#         @left, @right = nil, nil
#     end
# end

# @param {Integer[]} nums
# @return {TreeNode}
def construct_maximum_binary_tree(nums)
    
  return nil if nums.empty?

  maximum = nums.max
  max_index = nums.index(maximum)
  root = TreeNode.new(maximum)

  left_subarray_size = max_index
  right_subarray_size = nums.size - (left_subarray_size + 1)

  left_subarray = nums.first(left_subarray_size)
  right_subarray = nums.last(right_subarray_size)

  root.left = construct_maximum_binary_tree(left_subarray)
  root.right = construct_maximum_binary_tree(right_subarray)

  root
end
```

## 2018-12-09 更新

之前的遞迴寫法時間複雜度 best case: O(n log n), worst case: O(n^2)，但討論區爬到 [O(n)解法](https://leetcode.com/problems/maximum-binary-tree/discuss/106146/C%2B%2B-O(N)-solution)，遂以 Ruby 實作之：

```ruby
def construct_maximum_binary_tree(nums)
  stack = []
  nums.each do |element|
    current_node = TreeNode.new(element)
    while !(stack.empty?) && (stack.last.val < element) do
      current_node.left = stack.last
      stack.pop
    end
    stack.last.right = current_node if !(stack.empty?)
    stack << current_node
  end
  return stack.first
end
```

乍看之下，遍歷元素的同時還內含一個 while 迴圈，好像會上看 O(n^2)。但綜合所有 while 迴圈內容，每個元素只會 push/pop 一次，跟遍歷元素可以拆開來看，所以時間複雜度關係是相加而非相乘，得到 O(c1 * n + c2 * n) ~= O(n) 的結論。
