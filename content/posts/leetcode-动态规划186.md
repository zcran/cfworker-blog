---
title: "leetcode-动态规划186"
date: 2026-10-05T20:40:07+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 舒适的湿度

力扣嘉年华为了确保更舒适的游览环境条件，在会场的各处设置了湿度调节装置，这些调节装置受控于总控室中的一台控制器。 控制器中已经预设了一些调节指令，整数数组operate[i] 表示第 i 条指令增加空气湿度的大小。现在你可以将任意数量的指令修改为降低湿度（变化的数值不变），以确保湿度尽可能的适宜：

控制器会选择 一段连续的指令 ，从而进行湿度调节的操作；
这段指令最终对湿度影响的绝对值，即为当前操作的「不适宜度」
在控制器所有可能的操作中，最大 的「不适宜度」即为「整体不适宜度」

请返回在所有修改指令的方案中，可以得到的 最小 「整体不适宜度」。


```
impl Solution {
    pub fn un_suitability(operate: Vec<i32>) -> i32 {
        const INF: i32 = i32::MAX / 2;

        // mx 取所有值中最大值的两倍 + 1，保证可达湿度差范围在 [0, mx)
        let mx = (*operate.iter().max().unwrap() as usize) * 2 + 1;

        // pre[j]：处理到当前指令时，累计湿度差为 j 的最小不适宜度
        let mut pre = vec![INF; mx];
        pre[0] = 0;

        let mut f = vec![INF; mx];
        for &x in &operate {
            let x = x as usize;
            f.iter_mut().for_each(|v| *v = INF);

            for j in 0..mx {
                let dis = pre[j];
                if dis == INF {
                    continue;
                }
                // 选择加湿度：新湿度差 j + x，不适宜度取 max(dis, j + x)
                if j + x < mx {
                    let cand = dis.max((j + x) as i32);
                    if cand < f[j + x] {
                        f[j + x] = cand;
                    }
                }
                // 选择减湿度：新湿度差 |j - x|
                if j >= x {
                    if dis < f[j - x] {
                        f[j - x] = dis;
                    }
                } else {
                    let cand = dis - (j as i32) + (x as i32);
                    if cand < f[0] {
                        f[0] = cand;
                    }
                }
            }

            std::mem::swap(&mut pre, &mut f);
        }

        *pre.iter().min().unwrap()
    }
}
```
