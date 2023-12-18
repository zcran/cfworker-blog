---
title: "Rust Note 30"
date: 2023-06-04T22:12:33+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 二叉树的层序遍历

给你二叉树的根节点 root ，返回其节点值的 层序遍历 。 （即逐层地，从左到右访问所有节点）。

### C 解法：
```
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector <vector <int>> ret;
        if (!root) {
            return ret;
        }

        queue <TreeNode*> q;
        q.push(root);
        while (!q.empty()) {
            int currentLevelSize = q.size();
            ret.push_back(vector <int> ());
            for (int i = 1; i <= currentLevelSize; ++i) {
                auto node = q.front(); q.pop();
                ret.back().push_back(node->val);
                if (node->left) q.push(node->left);
                if (node->right) q.push(node->right);
            }
        }
        
        return ret;
    }
};
```


### Rust 解法：
```
use std::rc::Rc;
use std::cell::RefCell;
impl Solution {
	pub fn level_order(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<Vec<i32>> {
		let mut q = vec![];
		q.insert(0, root);
		let mut ans = vec![];
		while !q.is_empty() {
			let mut v = vec![];
			for i in 0..q.len() {
				if let Some(cur) = q.pop().unwrap(){
					v.push(cur.borrow().val);
					q.insert(0, cur.borrow_mut().left.take());
					q.insert(0, cur.borrow_mut().right.take());
				} 
			}
			if v.is_empty() {
				break;
			}
			ans.push(v);
		}
		ans
	}
}
```