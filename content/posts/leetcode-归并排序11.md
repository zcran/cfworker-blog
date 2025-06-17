---
title: "Leetcode 归并排序11"
date: 2025-05-23T21:22:01+08:00
tags: ["leetcode", "归并排序"]
draft: false
---

## 交易逆序对的总数

在股票交易中，如果前一天的股价高于后一天的股价，则可以认为存在一个「交易逆序对」。请设计一个程序，输入一段时间内的股票交易记录 record，返回其中存在的「交易逆序对」总数。

 

示例 1：

输入：record = [9, 7, 5, 4, 6]
输出：8
解释：交易中的逆序对为 (9, 7), (9, 5), (9, 4), (9, 6), (7, 5), (7, 4), (7, 6), (5, 4)。

```
impl Solution {
    pub fn reverse_pairs(mut record: Vec<i32>) -> i32 {
        let vec_len = record.len() as i32;
        if vec_len == 0 {
            return 0 as i32
        }
        let mut res:i32 = 0;
        Self::split_record(0,vec_len-1,&mut record,&mut res);
        res
    }

    pub fn merge(left:i32, middle:i32, right:i32, record:&mut Vec<i32>, res:&mut i32) {

        let leng = (right-left+1) as usize;
        let mut helper_vec = vec![0; leng];
        let mut i = 0 as usize;
        let mut l = left as usize;
        let mut r = (middle+1) as usize;

        while l <= middle as usize && r <= right as usize{
            let cmp = |(p1, p2)| if record[p1] <= record[p2] { p1 } else { p2 };
            let min_index = cmp((l, r));
            helper_vec[i] = record[min_index];
            i += 1;
            
            match min_index {
                p if p == l => {
                    l += 1;
                }
                _ => {
                    r += 1;
                    *res += middle -l as i32 + 1;
                }
            }
        }

        while l <= middle as usize{
            helper_vec[i] = record[l];
            i+=1;
            l+=1;
        }

        while r <= right as usize{
            helper_vec[i] = record[r];
            i+=1;
            r+=1;
        }

        for kk in 0..helper_vec.len() {
            record[left as usize+kk] = helper_vec[kk];
        }

    }

    pub fn split_record(left:i32, right:i32, record:&mut Vec<i32>, res:&mut i32){
        if(left >= right){
            return ;
        }

        let middle:i32 = left + ((right-left)>>1);
        //println!("middle is {}",middle);
        Self::split_record(left,middle,record,res);
        Self::split_record(middle+1,right,record,res);
        Self::merge(left,middle,right,record,res);
    }
}

```