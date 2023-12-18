---
title: "Rust Note 31"
date: 2023-06-10T19:51:15+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 将有序数组转换为二叉搜索树

给你一个整数数组 nums ，其中元素已经按 升序 排列，请你将其转换为一棵 高度平衡 二叉搜索树。

高度平衡 二叉树是一棵满足「每个节点的左右两个子树的高度差的绝对值不超过 1 」的二叉树。

### C 解法：
```
class Solution {
public:
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        return helper(nums, 0, nums.size() - 1);
    }

    TreeNode* helper(vector<int>& nums, int left, int right) {
        if (left > right) {
            return nullptr;
        }

        // 总是选择中间位置左边的数字作为根节点
        int mid = (left + right) / 2;

        TreeNode* root = new TreeNode(nums[mid]);
        root->left = helper(nums, left, mid - 1);
        root->right = helper(nums, mid + 1, right);
        return root;
    }
};
```


### Rust 解法：
```
use std::rc::Rc;
use std::cell::RefCell;
impl Solution {
    pub fn sorted_array_to_bst(nums: Vec<i32>) -> Option<Rc<RefCell<TreeNode>>> {
        fn make_tree(nums:& Vec<i32>, from:i32, to:i32) -> Option<Rc<RefCell<TreeNode>>>{
            match to-from{
                0 => None,
                1 => Some(Rc::new(RefCell::new(TreeNode::new(nums[from as usize])))),
                len => {
                    let center:i32 = (to+from)/2;
                    let mut node = TreeNode::new(nums[center as usize]);
                    node.left = make_tree(nums, from, center);
                    node.right = make_tree(nums, center+1, to);
                    Some(Rc::new(RefCell::new(node)))
                }
            }
        }
        make_tree(&nums,0,nums.len() as i32)
    }
}
```