---
title: 算法刷题笔记-草稿
top: 250
tags:
  - 笔记
  - 技能
categories:
  - 笔记
  - 专业笔记
  - 算法刷题
abbrlink: 23887
date: 2021-10-28 08:31:37
comments: false
description: 本笔记主要记录LeetCode、HZOJ平台上的数组、链表、哈希表、字符串、双指针、栈与队列、二叉树、回溯算法、贪心算法、动态规划、单调栈、深搜广搜等主要分支以及面试笔试中常考的题型。
---
## 数组

### 1、数组理论要点

-   **数组的元素是不能删除的，只能覆盖。**

### 2、重点题型

#### 2.1、二分查找

##### 2.1.1、普通二分

-   **适用条件**：区间内元素是**单调的、无重复的**

-   **算法要点**：注意**区间的循环不变量**

    -   **`[left, right]`**：循环判断条件对应`(left <= right)`，因为对于闭区间来说，`left`可能和`right`相等，同时对应的`right`值的更新应为`right = mid - 1`。
    -   **`[left, right)`**：循环判断条件对应`(left < right)`，因为对于左闭右开区间来说，`left`不可能和`right`相等，同时对应的`right`值的更新应为`right = mid`。

-   **算法模板**：

    ```C++
    //[left, right]
    int l = 0, r = nums.size() - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;  //可以避免left + right造成整形溢出
        if (nums[mid] == target) return mid;
        if (nums[mid] > target) right = mid - 1;
        else left = mid + 1;
    }
    //[left, right)
    int l = 0, r = nums.size();
    while (left < right) {
        int mid = left + (right - left) / 2;  //可以避免left + right造成整形溢出
        if (nums[mid] == target) return mid;
        if (nums[mid] > target) right = mid;
        else left = mid + 1;
    }
    ```

-   **相关题目**：

    -   ###### **[LC704. 二分查找](https://leetcode-cn.com/problems/binary-search/)**

    ```C++
    class Solution {
    public:
        //// [left, right]
        int search(vector<int>& nums, int target) {
            int l = 0, r = nums.size() - 1;
            while (l <= r) {
                int mid = l + (r - l) / 2;
                if (nums[mid] == target) return mid;
                if (nums[mid] > target) r = mid - 1;
                else l = mid + 1;
            }
            return -1;
        }
    };
    ```

    -   ###### **[LC35. 搜索插入位置](https://leetcode-cn.com/problems/search-insert-position/)**

    ```C++
    class Solution {
    public:
        int searchInsert(vector<int>& nums, int target) {
            int l = 0, r = nums.size() - 1;
            while (l <= r) {
                int mid = l + (r - l) / 2;
                if (nums[mid] == target) return mid;
                if (nums[mid] > target) r = mid - 1;
                else l = mid + 1;
            }
            // 此时 left > right
            // 目标值在数组所有元素之前 
            // 目标值插入数组中的位置
            // 目标值在数组所有元素之后的情况
            // 都可以返回 l
            return l;
        }
    };
    ```

    -   ###### [LC367.有效的完全平方数](https://leetcode-cn.com/problems/valid-perfect-square/)

    ```c++
    class Solution {
    public:
        bool isPerfectSquare(int num) {
            int l = 1, r = num;
            // [left, right]
            while (l <= r) {
                int mid = l + (r - l) / 2;
                if ((long long)mid * mid == num) return true;
                if ((long long)mid * mid < num) l = mid + 1;
                else r = mid - 1;
            }
            return false;
        }
    };
    ```

    -   ###### [HZOJ386.吃瓜群众](http://oj.haizeix.com/problem/386)

    ```c++
    #include <iostream>
    #include <algorithm>
    using namespace std;
    
    // 排序之后，位置就变了，需要使用cnt记住位置
    struct node {
        long long amount;
        int cnt;
    };
    
    int M, N, l, r;
    node num[100005];
    
    int bs(long x) {
        l = 0, r = M - 1;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (num[mid].amount == x) return num[mid].cnt;
            if (num[mid].amount > x) r = mid - 1;
            else l = mid + 1;
        }
        return 0;
    }
    
    bool cmp(const node &a, const node &b) {
        return a.amount < b.amount;
    }
    
    int main() {
        cin >> M >> N;
        for (int i = 0; i < M; i++) {
            cin >> num[i].amount;
            num[i].cnt = i + 1;
        }
        sort(num, num + M, cmp);
        for (int i = 0; i < N; i++) {
            int temp;
            cin >> temp;
            cout << bs(temp) << endl;
        }
            
        return 0;
    }
    ```


##### 2.1.2、特殊二分

-   **适用条件**：区间内元素是单调的，题中要求的是满足条件的第一个或最后一个元素的位置

-   **算法要点**：
	
	-   根据题意确定是`11110000`还是`00001111`类型
	
-   **算法模板**：
    ```c++
    // 00001111型：选取最左边的1
    while (l != r) {
        int mid = l + (r - l) / 2; //上取整：尽量多的满足1的情况
        if (func(mid) <= target) r = mid;  //为1的情况
        else l = mid + 1; //为0的情况
    }
    
    // 11110000型：选取最右边的1
    while (l != r) {
        int mid = l + (r - l + 1) / 2; //下取整：尽量多的满足1的情况
        if (func(mid) >= target) l = mid; //为1的情况
        else r = mid - 1; //为0的情况
    }
    ```
	
-   **相关题目**：

    -   ###### **[LC69. Sqrt(x)](https://leetcode-cn.com/problems/sqrtx/)**

    ```c++
    class Solution {
    public:
        int mySqrt(int x) {
            long long l = 0, r = x;
            // 类型：11110000
            while (l != r) {
                long long mid = l + (r - l + 1) / 2;
                if (mid * mid <= x) l = mid;
                else r = mid - 1; 
            }
            return l;
        }
    };
    ```
    
    -   ###### [HZOJ387.吃瓜群众升级版](http://oj.haizeix.com/problem/387)
    
    ```c++
    // 分析：
    // 类型：00001111
    
    #include <iostream>
    #include <algorithm>
    using namespace std;
    
    // 排序之后，位置就变了，需要使用cnt记住位置
    struct node {
        long long amount;
        int cnt;
    };
    
    int M, N, l, r;
    node num[100005];
    
    int bs(long x) {
        l = 0, r = M - 1;
        while (l != r) {
            int mid = l + (r - l) / 2;
            if (num[mid].amount >= x) r = mid;
            else l = mid + 1;
        }
        return num[l].amount >= x ? num[l].cnt : 0;
    }
    
    bool cmp(const node &a, const node &b) {
        return a.amount < b.amount;
    }
    
    int main() {
        //cin >> M >> N;
        scanf("%d%d", &M, &N);
        for (int i = 0; i < M; i++) {
            //cin >> num[i].amount;
            scanf("%lld", &num[i].amount);
            num[i].cnt = i + 1;
        }
        sort(num, num + M, cmp);
        for (int i = 0; i < N; i++) {
            long long temp;
            //cin >> temp;
            scanf("%d", &temp);
            //cout << bs(temp) << endl;
            printf("%d\n", bs(temp));
        }
            
        return 0;
    }
    ```
    
    -   ###### [LC34.在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)
    
    ```c++
    class Solution {
    public:
        vector<int> searchRange(vector<int>& nums, int target) {
            if (nums.size() == 0) return vector<int>{-1, -1};
            
            //用特殊二分确定目标值的起始位置
            //类型：00001111
            int l = 0, r = nums.size() - 1;
            while (l != r) {
                int mid = l + (r - l) / 2;
                if (nums[mid] >= target) r = mid;
                else l = mid + 1;
            }
            if (nums[l] != target) return vector<int>{-1, -1};
            
            //从起始位置遍历到结束的位置
            while (r < nums.size()) {
                if (nums[r] != target) break;
                r += 1;
            }
            return vector<int>{l, r - 1};
        }
    };
    ```

##### 2.1.3、二分答案

-   **适用条件**：答案满足单调性，题中要求的是**最大的最小或者最小的最大**

-   **算法要点**：确认以下4点
    -   **分谁**：分的是题目中要求得是谁
    -   **求谁**：由**分谁**可以直接唯一确定的值就是**求谁**，**求谁**就是答案，答案需要是单调的
    -   **确认左右边界，更新动态指针**
    -   **确定是二分的哪种特殊情况**：11110000  或  00001111
    
-   **算法模板**：
    
    -   套用特殊二分的算法模板
    
-   **相关题目**：
    -   ###### [HZOJ389. 暴躁的程序猿](http://oj.haizeix.com/problem/389)
    ```c++
    // 分析：
    // 分谁：题中求距离，按照距离分
    // 求谁：距离的不同会导致能够安排的员工数量不一样，答案就是员工数量
    // 距离：1  2  3  4  5  6
    // 数量：3  3  3  2  2  2
    // 类型：1  1  1  0  0  0
    // 左边界：距离最小为1 ==>> l = 1
    // 右边界：距离最大为两端之间的距离 ==>> r = num[m - 1] - num[0]
        
    #include <iostream>
    #include <algorithm>
    using namespace std;
    
    
    int m, n, num[100005], l, r;
    
    //二分答案最难的部分：答案的计算方法
    int func(int mid) {
        int ans = 1, last = num[0];
        for (int i = 1; i < m; i++) {
            if (last + mid <= num[i]) {
                ans += 1;
                last = num[i];
            }
        }
        return ans;
    }
    
    int main() {
        cin >> m >> n;
        for (int i = 0; i < m; i++) cin >> num[i];
        sort(num, num + m);
        l = 1, r = num[m - 1] - num[0];
        while (l != r) {
            int mid = l + (r - l + 1) / 2;
            if (func(mid) >= n) l = mid;
            else r = mid - 1;
        } 
        cout << l << endl;    
        return 0;
    }
    ```
    
    -   ###### [HZOJ390.原木切割](http://oj.haizeix.com/problem/390)
    
	```c++
    // 分析：
    // 分谁：题目中要求的是每一小段原木的最大长度，按照每一小段长度不同进行分，分谁指的是段长
    // 求谁：小段原木的长度不同，切成的总数量就不一样，求谁指的是数量
    // 段长：1   2   3   4  5  6  7  8
    // 数量：43  21  14  9  8  6  5  3
    // 类型：1   1   1   1  1  0  0  0
    // 左边界：段长最小为1 ==>> l = 1
    // 右边界：段长最大为最长原木的长度 ==>> r = max(r, num[i])
    
    #include <iostream>
    using namespace std;
    
    int n, m, num[100005], l = 1, r = 1;
    //答案的计算方法
    int func(int mid) {
        int ans = 0;
        for (int i = 0; i < n; i++) ans += num[i] / mid;
        return ans;
    }
    
    int main() {
        cin >> n >> m;
        for (int i = 0; i < n; i++) {
         	cin >> num[i];
            r = max(r, num[i]);  //分谁：段长，段长最大值只能是原木中最长的
        }  
        while (l != r) {
            int mid = l + (r - l + 1) / 2;
            if (func(mid) >= m) l = mid;
            else r = mid - 1;
        }   
        cout << l << endl;  
        return 0;
    }
    ```

    -   ###### [HZOJ391. 数列分段](http://oj.haizeix.com/problem/391)
    
    ```c++
    // 分析：
    // 分谁：求的是每段和的最大值，按照每段和最大值进行划分
    // 求谁：每段和的最大值不同，影响分的段数，答案是段数
    // 最值： 5  6  7  8  9  10  11
    // 段数： 5  3  3  3  3  2   2
    // 类型： 0  1  1  1  1  1   1   //可以根据题目反推类型，最大的最小就是00001111型
    // 左边界：数组中的最大值 ==>> l = max(l, num[i])
    // 右边界：数组所有元素的和 ==>> r += num[i]
    
    #include <iostream>
    using namespace std;
    
    int n, m;
    long long num[100005], l, r;
    
    //最难的部分：答案的计算方法
    int func(long long mid) {
        int cnt = 0;
        long long now = 0;
        for (int i = 0; i < n; i++) {
            if (now + num[i] > mid) {
                cnt += 1;
                now = num[i];
            } else if (now + num[i] == mid) {
                cnt += 1;
                now = 0;
            } else {
                now += num[i];
            }
        }
        if (now) cnt += 1;
        return cnt;
    }
    
    int main() {
        cin >> n >> m;
        for (int i = 0; i < n; i++) {
            cin >> num[i];
            l = max(l, num[i]);
            r += num[i];
        }
        while (l != r) {
            long long mid = l + (r - l) / 2;
            if (func(mid) <= m) r = mid;
            else l = mid + 1;
        }
        cout << l << endl;
        return 0;
    }
    ```

    -   ###### [HZOJ392. 丢瓶盖](http://oj.haizeix.com/problem/392)

	```c++
    // 分析：
    // 分谁：求的是距离，按照距离划分
    // 求谁：距离影响的是需要找出的瓶盖数，答案是瓶盖数量
    // 距离： 1  2  3  4  5  6  7
    // 数量： 5  3  2  2  1  1  1
    // 类型： 1  1  0  0  0  0  0
    // 左边界：距离的最小值为1 ==>> l = 1
    // 右边界：距离的最大值为两端之差 ==>> num[A - 1] - num[0] + 1 
    #include <iostream>
    #include <algorithm>
    using namespace std;
    
    int A, B;
    long long num[100005], l = 1, r;
    //答案的计算方法
    int func(long long mid) {
        int cnt = 1;
        long long last = num[0];
        for (int i = 1; i < A; i++) {
            if (last <= num[i] - mid) {
                cnt += 1;
                last = num[i];
            }
        }
        return cnt;
    }
    
    int main() {
        cin >> A >>  B;
        for (int i = 0; i < A; i++) 
            cin >> num[i];
        sort(num, num + A);
        r = num[A - 1] - num[0] + 1;
        while (l != r) {
            long long mid = l + (r - l + 1) / 2;
            if (func(mid) >= B) l = mid;
            else r = mid - 1;
        }
        cout << l << endl;
        return 0;
    }
	```

    -   ###### [HZOJ393.切绳子](http://oj.haizeix.com/problem/393)

	```c++
    // 分析：
    // 分谁：求的是每段绳子的最长长度，按照绳子的长度划分
    // 求谁：绳子长度影响切出来的数量，答案是数量
    // 长度：  1  2  3  4  5  6
    // 数量：  28 13 7  6  3  2
    // 类型：  1  1  0  0  0  0
    // 左边界：0.0即可
    // 右边界：最大绳子的长度 ==>> r = max(r, num[i])
    // 特别：对于浮点数的二分，在求mid时是不区分上取整和下取整的，并且l,r是不需要考虑+/-1
    
    #include <iostream>
    #include <cstdio>
    using namespace std;
    
    int n, k;
    double num[100005], l, r;
    //答案的计算方法
    int func(double mid) {
        int cnt = 0;
        for (int i = 0; i < n; i++) 
            cnt += (int)(num[i] / mid);
        return cnt;
    }
    
    int main() {
        cin >> n >> k;
        for (int i = 0; i < n; i++) {
            cin >> num[i];
            r = max(r, num[i]);
        }
        while (r - l > 0.00001) {
            double mid = l + (r - l) / 2;
            if (func(mid) >= k) l = mid;
            else r = mid;
        }
        printf("%.2f\n", l - 0.005); //printf可以设置格式的输出，-0.005再加上四舍五入即可满足题目要求
        return 0;
    }
	```

    -   ###### [HZOJ394.跳石头](http://oj.haizeix.com/problem/394)

	```c++
    // 分析：
    // 分谁：求的是最短跳跃距离，按照最短跳跃距离划分
    // 求谁：最短跳跃距离影响的是移走石头的数量，答案是移走石头的数量
    // 距离：0  1  2  3  4  5  6  7  8  9
    // 数量：0  0  0  1  2  3  3  4  4  4
    // 类型：1  1  1  1  1  0  0  0  0  0  
    // 左边界：跳跃的最短距离可以为0 ==>> l = 0
    // 右边界：跳跃距离的最大值不能超过终点到起点的距离 ==>> r = L
    
    #include <iostream>
    using namespace std;
    
    long long L, num[50005], l, r;
    int M, N;
    
    int func(long long mid) {
        int cnt = 0;
        long long last = 0;
        for (int i = 0; i <= M; i++) {
        	if (last + mid > num[i]) {
            	cnt += 1;
            } else {
                last = num[i];
            }
        }
        return cnt;    
    }
    
    int main() {
        cin >> L >> M >> N;
        for (int i = 0; i < M; i++) cin >> num[i];
        num[M] = L;
        r = L;
        while (l != r) {
            long long mid = l + (r - l + 1) / 2;
            if (func(mid) <= N) l = mid;
            else r = mid - 1;
        }
        cout << l << endl;
        return 0;
    }
    ```
	
	-   ###### [HZOJ395.复制书稿](http://oj.haizeix.com/problem/395)
    
	```c++
	// 分析：
	// 分谁：求的是复制时间，按照复制时间进行划分
	// 求谁：复制时间影响的抄写的人数，答案是人数
	// 时间：12  13  14  15  16  17  18  19 
	// 人数：5   5   5   4   4   3   3   3
	// 类型：0   0   0   0   0   1   1   1  
	// 左边界：复制时间的最小值就是一人负责一本，最后的那一本需要的时间 ==>> l = max(l, num[i])
	// 右边界：复制时间的最大值就是一个人复制所有 ==>> r += num[i]
       
	#include <iostream>
	using namespace std;
	int m, k, num[505], res[505][2], l, r;
	int func(int mid) {
	    int cnt = 0, now = num[0];
	    for (int i = 1; i < m; i++) {
	        if (now + num[i] < mid) {
	            now += num[i];
            } else if (now + num[i] == mid) {
	            cnt += 1;
	            now = 0;
	        } else {
	            now = num[i];
	            cnt += 1;
	        }
	    }
	    if (now) cnt += 1;
	    return cnt;
	}
       
	int main() {
	    cin >> m >> k;
	    for (int i = 0; i < m; i++) {
	        cin >> num[i];
	        r += num[i];
	        l = max(l, num[i]);
	    }

	    while (l != r) {
	        int mid = l + (r - l) / 2;
	        if (func(mid) <= k) r = mid;
	        else l = mid + 1;
	    }

	    //题中要求尽量让后面的人多抄，需要从后往前遍历
	    int j = k, now = num[m - 1];
	    for (int i = m - 2; i >= 0; i--) {
	        if (now + num[i] > l) {
	            res[j][0] = i + 2; // i + 1 + 1
	            j--; // 去掉一个人
	            now = num[i]; 
	            res[j][1] = i + 1; // 下一个人的终止位置
	        } else if (now + num[i] == l) {
	            res[j][0] = i + 1;
	            j--;
	            i--; //这本书已经被抄过了，要到下一本书了
	            now = num[i];
	            res[j][1] = i + 1; // 下一个人的终止位置
	        } else {
	            now += num[i];
	        }
	    }
	    res[1][0] = 1; //第一个人抄的起始位置
	    res[k][1] = m; //最后一个人抄的终止位置

	    for (int i = 1; i <= k; i++) cout << res[i][0] << " " << res[i][1] << endl;
	    return 0;
	}
    ```
    
-   ###### [LC278. 第一个错误的版本](https://leetcode-cn.com/problems/first-bad-version/)
    
	```c++
	// The API isBadVersion is defined for you.
	// bool isBadVersion(int version);  
    
	class Solution {
	    public:
	    int firstBadVersion(int n) {
	        int l = 0, r = n;
    
	        // 分析：
	        // 检测最先坏的版本，属于00001111型
    
	        while ( l != r) {
	            int mid = l + (r - l) / 2; //向上取整
	            if (isBadVersion(mid)) r = mid;
	            else l = mid + 1;
	        }
	        return l;
	    }
	};
    ```

##### 2.1.4、二分查找总结

-   ###### **二分的本质**

    **删掉不存在答案的区间**

-   ###### **整数范围**

    1.  **普通二分**：
        -   注意`区间的循环不变量`，确定`循环条件`，最好选择一种，长期坚持使用，本人习惯使用`[left, right]`
        -   中间值的计算方法，采用`int mid = l + (r - l) / 2`方式，避免整形溢出
    	-   对于`r = mid - 1`还是`l = mid + 1`要考虑清楚，还是在特殊情况下会发生变化  
    2.  **特殊二分**： 
        -   根据题意确定是`00001111`还是`11110000`类型
        -   根据类型套用模板        
        -   类型模板：注意区分是`下取整`还是`上取整`，是`r = mid; l = mid + 1;`还是`l = mid; r = mid - 1;`
            ```c++
            // 00001111型：选取最左边的1
            while (l != r) {
                int mid = l + (r - l) / 2; //上取整：尽量多的满足1的情况
                if (func(mid) <= target) r = mid;  //为1的情况
                else l = mid + 1; //为0的情况
            }
            
            // 11110000型：选取最右边的1
            while (l != r) {
                int mid = l + (r - l + 1) / 2; //下取整：尽量多的满足1的情况
                if (func(mid) >= target) l = mid; //为1的情况
            	else r = mid - 1; //为0的情况
            }
            ```
    3.  **二分答案**：
        -   根据题意按照如下顺序进行分析：
            1.  分谁：
            2.  求谁：
            3.  “分谁”：在分谁中确定的量
            4.  “求谁”：在求谁中确定的量
            5.  类型：  根据提议正推类型，或者根据题目中要求的反向推类型：最大的最小是`11110000`型，最小的最大是`00001111`型
            6.  左边界：
            7.  右边界：  
        -   根据类型套用特殊二分的类型模板

-   ###### **浮点数范围**

    1.  **普通二分**：
        -   循环条件使用`精度`来控制
        -   中间值的计算方法：`double mid = l + (r - l) / 2;`
        -   对于`r, l`的变更全部采用`r = mid, l = mid`
    2.  **特殊二分**：
        -   循环条件使用`精度`来控制
        -   中间值的计算不存在上取整和下取整之说，统一使用`double mid = l + (r - l) / 2;`
        -   对于`r, l`的变更全部采用`r = mid, l = mid`
    3.  **二分答案**：(与整数不同的地方)
        -   循环条件使用`精度`控制
        -   中间值的计算不存在上取整和下取整之说，统一使用`double mid = l + (r - l) / 2;`
        -   对于`r, l`的变更全部采用`r = mid, l = mid`

>   **注意**：二分查找的使用条件：单调，有时需要对给的数据进行`sort`操作

##### 2.1.5、牛顿迭代法

-   **适用情景**：求高阶方程根的问题

-   **递推公式**：x~2~ = x~1~ - f(x~1~) / f^‘^(x~1~)

-   **函数模板**：

    ```c++
    type newton(type (*F)(type, ...), type (*f)(type, ...), type x, ...){
        /*
         F：原函数  
         f：原函数的导函数  
         x, ...`：原函数中的系数  
         高阶方程根的求解判断依据：`使用精度判断迭代次数`，要使用`fabs(F(x,n))`来判断精度  
         */
    }
    ```
    
-   **相关样例**：

    ```c++
    double func(double x, double n) {
        return x * x - n;
    }
    double f(double x) {
        return 2 * x;
    }
    double newton(double (*F)(double, double), double (*f)(double), double n) {
        double x = n / 2.0;  //随便取，但不能取0
        #define EPLS 1e-7
        while (fabs(F(x, n)) > EPLS) {   
            x -= F(x, n) / f(x);   //循环迭代
        }
        #undef EPLS
        return x;
    }
    ```

#### 2.2、移除元素

-   **解题思路**：数组的元素在内存地址中是连续的，不能单独删除数组中的某个元素，只能覆盖来移除元素。

-   **解题方法**：
	
	-   双指针法：**快指针按照正常速度遍历数组，慢指针只有满足条件时再移动，慢指针控制新数组的长度**
	
-   **相关题目**：
	-   ##### [LC27.移除元素](https://leetcode-cn.com/problems/remove-element/)
    
    ```c++
    class Solution {
    public:
        int removeElement(vector<int>& nums, int val) {
            int slow = 0, fast = 0;
            for ( ; fast < nums.size(); fast++) { //fast正常速度遍历数组
                if (nums[fast] == val) continue;
                //满足条件时，更新slow位置上的值，再更新slow的位置
                nums[slow] = nums[fast];
                slow += 1;
            }
            return slow;  //此时slow的位置为最后一个元素的下标+1，也就是数组的长度值
        }
    };
    ```
    -   ##### [LC26.删除有序数组中的重复项](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)
    ```c++
    class Solution {
    public:
        int removeDuplicates(vector<int>& nums) {
            
            // 分析：本题和27题的区别是val没有只要，需要一直更新
            // val更新的位置：val应为slow标记的新数组的元素，需要在新数组元素更新后更新val
            int slow = 0, fast = 0, val = INT_MAX; //val = INT_MAX 保证第一个元素不被覆盖
            for ( ; fast < nums.size(); fast++) {
                if (nums[fast] == val) continue;
                nums[slow] = nums[fast];
                val = nums[slow]; //新数组元素更新后更新val
                slow += 1;     
            }
            return slow;
        }
    };
    ```
    -   ##### [LC283.移动零](https://leetcode-cn.com/problems/move-zeroes/)
    ```c++
    class Solution {
    public:
        void moveZeroes(vector<int>& nums) {
            int slow = 0, fast = 0;
            for ( ; fast < nums.size(); fast++) {
                if (nums[fast] == 0) continue;
                nums[slow] = nums[fast];
                slow += 1;
            }
            for (int i = slow; i < nums.size(); i++) nums[i] = 0; //后面补零
            return ;
        }
    };
    ```
    -   ##### [LC844.比较含退格的字符串](https://leetcode-cn.com/problems/backspace-string-compare/)
    ```c++
	class Solution {
	public:
	    bool backspaceCompare(string s, string t) {
            
	        // 解法：用S_skip, T_skip来记录s, t串最后位置的#个数，每次循环先处理回退，再比较对应
	        //      位置是否相同
            
	        int S_skip = 0, T_skip = 0, l = s.size() - 1, r = t.size() - 1;
	        while (1) {
	            while (l >= 0) {
	                if (s[l] == '#') S_skip += 1; // 如果是#，记下个数
	                else {
	                    if (S_skip) S_skip -= 1; //处理回退
	                    else break;
	                }
	                l -= 1; // 勿忘
	            }
	            while (r >= 0) {
	                if (t[r] == '#') T_skip += 1;
	                else {
	                    if (T_skip) T_skip -= 1;
	                    else break;
	                }
	                r -= 1;
	            }
	            if (l < 0 || r < 0) break; //比较
	            if (s[l] != t[r]) return false;
	            l -= 1, r -= 1; //勿忘
	        }
	        if (l == -1 && r == -1) return true;
	        return false;
	    }
	};
    ```
    -   ##### [LC977.有序数组的平方](https://leetcode-cn.com/problems/squares-of-a-sorted-array/)
    ```C++
    class Solution {
    public:
        vector<int> sortedSquares(vector<int>& nums) {
            vector<int> res(nums.size(), 0);
            int slow = 0, fast = nums.size() - 1;  // 双指针
            for (int ind = nums.size() - 1; ind >= 0; ind--) {
                if (abs(nums[slow]) > abs(nums[fast])) {
                    res[ind] = nums[slow] * nums[slow];
                    slow += 1;
                } else {
                    res[ind] = nums[fast] * nums[fast];
                    fast -= 1;
                }
            }
            return res;
        }
    };
    ```
    
-   ##### **移除元素总结**

    1.  解题方法：双指针
    2.  方法思想：(特别是对**数组元素按条件覆盖移除问题**，像LC26、27、283题型)
        -   快指针正常遍历数组
        -   慢指针满足条件时在更新，同时用快指针指向的值进行覆盖

#### 2.3、滑动窗口法

-   **算法概念**：不断的调节子序列的起始位置和终止位置，从而得出我们要想的结果

-   **算法要点**：确定如下3点
    
    1.  窗口内是什么
    2.  如何移动窗口的起始位置：关键之处
    3.  如何移动窗口的结束位置
    
-   **算法优点**：根据当前子序列和大小的情况，不断调节子序列的起始位置，从而降低时间复杂度

-   **相关题目**：

    -   ##### [LC209.长度最小的子数组](https://leetcode-cn.com/problems/minimum-size-subarray-sum/)

    ```c++
    // 分析：
    // 窗口内是什么：满足和>= target的长度最小的连续子数组
    // 起始位置移动：如果当前窗口值大于target时，窗口向前移动
    // 结束位置移动：遍历整个数组
    
    class Solution {
    public:
        int minSubArrayLen(int target, vector<int>& nums) {
            int sum = 0; //用来限制窗口的大小
            int res = INT_MAX;
            int i = 0; //窗口的起始位置
            int subLength = 0; //记录窗口的长度
            for (int j = 0; j < nums.size(); j++) {
                sum += nums[j]; //先扩大窗口，使其满足条件
                while (sum >= target) { //此处调整窗口的起始位置，需要使用while，求的是最小子数组
                    subLength = j - i + 1;
                    res = min(subLength, res); //更新答案
                    sum -= nums[i]; //
                    i += 1; //更新窗口起始位置
                } 
            }
            return res == INT_MAX ? 0 : res;
        }
    };
    ```

    -   ##### [LC904.水果成篮](https://leetcode-cn.com/problems/fruit-into-baskets/)

    ```c++
    // 分析：
    // 窗口内是什么：满足只有两种果子的连续子数组
    // 起始位置移动：如果当前窗口内超过两种果子时，窗口向前移动
    // 结束位置移动：遍历整个数组
    class Solution {
    public:
        int totalFruit(vector<int>& fruits) {
            int cnt = 0;
            int res = INT_MIN;
            int i = 0;
            unordered_map<int, int> umap; //记录果子种类
            for (int j = 0; j < fruits.size(); j++) {
                umap[fruits[j]] += 1;
                while (umap.size() > 2) {//窗口起始位置的移动处理
                    umap[fruits[i]] -= 1;
                    if (umap[fruits[i]] == 0) umap.erase(fruits[i]); //只有这种果子全清才可以
                    cnt -= 1;
                    i += 1;
                }
                cnt += 1;
                res = max(res, cnt);
            }
            return res == INT_MIN ? 0 : res;
        }
    };
    ```

    -   ##### [LC76.最小覆盖子串](https://leetcode-cn.com/problems/minimum-window-substring/)

    ```c++
    class Solution {
    public:
    unordered_map<char, int> umap, ucnt; //umap记录子串的情况，ucnt记录窗口情况
    
        bool check() {
            for (auto&[ch, cnt] : umap) 
                if (ucnt[ch] < cnt) return false;
            return true;
        }
    
        string minWindow(string s, string t) {
            int l = 0;
            int len = INT_MAX;
            int lans = -1, rans = -1;  //由于要返回的是子串，用lans和rans记录子串的位置
    
            for (auto i : t) umap[i] += 1;//记录子串的字母情况
    
            for (int i = 0; i < s.size(); i++) { //进行滑动窗口的操作
                if (umap.find(s[i]) != umap.end()) ucnt[s[i]] += 1;
                while (check()) { //检查是不是包含子串
                    if (len > i - l + 1) { 
                        lans = l; //标记新的结果子串的位置
                        rans = i;
                        len = i - l + 1;
                    }
                    if (umap.find(s[l]) != umap.end()) 
                        ucnt[s[l]] -= 1;  
                    l++; //移动滑动窗口的起始位置
                }
            }
            return lans == -1 ? "" : s.substr(lans, len);
        }
    };
    ```
    
-   ##### **滑动窗口总结**

    1.  分析要点：

        -   窗口内是什么
        -   窗口的起始位置何时移动
        -   窗口的终止位置何时移动

    2.  算法模板：

        ```c++
        int i = 0; //窗口的起始位置
        
        for (int j = 0; j < nums.size(); j++) { //遍历整个数组
            /* 窗口终止位置后移后，对窗口内新增加元素的处理 */
            while (...) {	// 窗口内需要满足的条件,循环体中更新窗口的位置,一定要用while
            	/*
            	更新相应的值
            	*/
                i += 1; //窗口的起始位置后移
            }
            /*
            其他操作
            */
        } 
        ```
        

#### 2.4、螺旋矩阵

-   **解题要点**：区间的循环不变量，本人采用[left, right)

-   **解题思路**：模拟顺时针画矩阵的过程

    1.  填充上行从左到右
    2.  填充右列从上到下
    3.  填充下行从右到左
    4.  填充左列从下到上

-   **相关题目**：

    -   ##### [LC59.螺旋矩阵 II](https://leetcode-cn.com/problems/spiral-matrix-ii/)

    ```c++
    class Solution {
    public:
        vector<vector<int>> generateMatrix(int n) {
            vector<vector<int>> res(n, vector<int>(n, 0));
    
            int startx = 0, starty = 0; // 每循环一个圈的起始位置，最一开始从[0,0]位置开始
            int loop = n / 2; //总循环的次数，n为奇数，循环n/2次，中间位置(n/2, n/2)须单独处理
            int mid = n / 2; //n为奇数才会有中间位置(n/2, n/2)
            int count = 1; //用来给矩阵赋值
            int offset = 1; //每一圈循环，需要控制每一条边遍历的长度
            int i, j; 
            while (loop--) { 
                i = startx;  //从起点开始遍历
                j = starty;
    			
                //模拟填充上行从左到右[左闭右开）
                for ( ; j < starty + n - offset; j++) {
                    res[i][j] = count;
                    count += 1;
                }
                //模拟填充右列从上到下[左闭右开）
                for ( ; i < startx + n - offset; i++) {
                    res[i][j] = count;
                    count += 1;
                }
                //模拟填充下行从右到左[左闭右开）
                for ( ; j > starty; j--) {
                    res[i][j] = count;
                    count += 1;
                }
                //模拟填充左列从下到上[左闭右开）
                for ( ; i > startx; i--) {
                    res[i][j] = count;
                    count += 1;
                }
    
                startx += 1; //更新下一轮循环的起点
                starty += 1;
    
                offset += 2; //更新下一轮循环的每一条边遍历需要去除的长度
            }
    
            if (n % 2) res[mid][mid] = count; //处理中心位置
            return res;
        }
    };
    ```

    -   ##### [LC54.螺旋矩阵](https://leetcode-cn.com/problems/spiral-matrix/)

    ```c++
    class Solution {
    public:
        vector<int> spiralOrder(vector<vector<int>>& matrix) {
            int m = matrix.size(), n = matrix[0].size();
            vector<int> res;
            int startx = 0, starty = 0;
            int loop = min(m, n) / 2;
            int offset = 1;
            int i, j;
            while (loop--) {
                i = startx;
                j = starty;
    
                for ( ; j < starty + n - offset; j++) res.push_back(matrix[i][j]);
                for ( ; i < startx + m - offset; i++) res.push_back(matrix[i][j]);
                for ( ; j > starty; j--) res.push_back(matrix[i][j]);
                for ( ; i > startx; i--) res.push_back(matrix[i][j]);
    
                startx += 1;
                starty += 1;
    
                offset += 2;
            }
            if (min(n, m) % 2) { // 特殊处理 自己画一画找找规律
                if (m > n) {
                    //上面循环体中，最终startx未赋值给i，同时使用[]，遍历整行剩余的元素
                    for (i = startx ; i <= startx + m - offset; i++) 
                        //上面循环体中，最终starty未赋值给j
                        res.push_back(matrix[i][starty]); //注意细节处理
                } else {
                    //上面循环体中，最终starty未赋值给j，同时使用[]，遍历整列剩余的元素
                    for (j = starty ; j <= starty + n - offset; j++) 
                        //上面循环体中，最终starty未赋值给j
                        res.push_back(matrix[startx][j]); //注意细节处理
                }
            }
            return res;
        }
    };
    ```

-   ##### **螺旋矩阵的总结**

    1.  记住`LC59`题的模拟顺序

## 链表

### 1.链表的理论要点

- 需要返回整个链表时，借助`虚拟头节点`。
### 2、重点题型

#### 2.1、链表元素的移除

- **解题思想**：

    - 需要返回链表时使用虚拟头节点指向头节点
    - 对链表的节点删除操作时，需要定义指向待删除节点前面的一个节点，使用cur->next指向待删除的节点，方便删除操作
    - 删除链表节点时，需要释放掉被删除的节点

- **相关题目**：

    - ##### [LC203.移除链表元素](https://leetcode-cn.com/problems/remove-linked-list-elements/)

    ```c++
    /**
     * Definition for singly-linked list.
     * struct ListNode {
     *     int val;
     *     ListNode *next;
     *     ListNode() : val(0), next(nullptr) {}
     *     ListNode(int x) : val(x), next(nullptr) {}
     *     ListNode(int x, ListNode *next) : val(x), next(next) {}
     * };
     */
    class Solution {
    public:
        ListNode* removeElements(ListNode* head, int val) {
            ListNode *dummyHead = new ListNode(-1, head); // 定义虚拟头节点
            ListNode *cur = dummyHead;
            while (cur->next) { // 循环条件一般看循环体中的实现来确定
                if (cur->next->val != val) { //使用cur->next指向待删除的元素
                    cur = cur->next;
                    continue;
                }
                ListNode *tmp = cur->next;  //方便释放节点
                cur->next = cur->next->next; //这里可以体现使用cur->next指向待删除节点的遍历
                delete tmp;
            }
            head = dummyHead->next; 
            delete dummyHead; //释放虚拟头节点
            return head;
        }
    };
    ```

    -   ##### [LC237.删除链表中的节点](https://leetcode-cn.com/problems/delete-node-in-a-linked-list/)

    ```c++
    /**
     * Definition for singly-linked list.
     * struct ListNode {
     *     int val;
     *     ListNode *next;
     *     ListNode(int x) : val(x), next(NULL) {}
     * };
     */
class Solution {
    public:
    void deleteNode(ListNode* node) {
            node->val = node->next->val; //用后面一个节点值替代node的值，删除后面的节点
            ListNode *tmp = node->next;
            node->next = node->next->next;
        delete tmp;
            return ;
    }
    };
    ```
    
    -   ##### [LC83.删除排序链表中的重复元素](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)
    
    ```c++
    /**
     * Definition for singly-linked list.
     * struct ListNode {
     *     int val;
     *     ListNode *next;
     *     ListNode() : val(0), next(nullptr) {}
     *     ListNode(int x) : val(x), next(nullptr) {}
     *     ListNode(int x, ListNode *next) : val(x), next(next) {}
     * };
     */
    class Solution {
    public:
        ListNode* deleteDuplicates(ListNode* head) {
            ListNode *p = head;
            while (p && p->next) {
                if (p->val == p->next->val) {
                    ListNode *tmp = p->next;
                    p->next = p->next->next;
                    delete tmp;
                } else {
                    p = p->next;
                }
            } 
            return head;
        }
    };
    ```

    -   ##### [LC82.删除排序链表中的重复元素 II](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)
    
    ```c++
    /**
     * Definition for singly-linked list.
     * struct ListNode {
     *     int val;
     *     ListNode *next;
     *     ListNode() : val(0), next(nullptr) {}
     *     ListNode(int x) : val(x), next(nullptr) {}
     *     ListNode(int x, ListNode *next) : val(x), next(next) {}
     * };
     */
    class Solution {
    public:
        ListNode* deleteDuplicates(ListNode* head) {
            ListNode *dummyHead = new ListNode(-1, head); //需要返回链表，使用虚拟头节点
            ListNode *l = dummyHead; 
            while (l->next) {//由于循环体中用到l->next->next，此处的判断条件要为l->next
                ListNode *r = l->next->next; 
                while (r && r->val == l->next->val) { //去除重复的元素
                    ListNode *tmp = r;
                    r = r->next;//r指向重复节点后面的一位
                    delete tmp;
                }
                if (l->next->next == r) l = l->next; // 没有重复节点，l = l->next
                else {
                    ListNode *tmp = l->next;
                    l->next = r; // 有重复节点，l->next = r
                    delete tmp;
                }    
            }
            head = dummyHead->next;
            delete dummyHead;
            return head;
        }
    };
    ```
    
    -   ##### [LC19.删除链表的倒数第 N 个结点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)
    
        ![image-20211103231204267](https://dongqing1903.oss-cn-shanghai.aliyuncs.com/img/image-20211103231204267.png)
    
    ```c++
    /**
     * Definition for singly-linked list.
     * struct ListNode {
     *     int val;
     *     ListNode *next;
     *     ListNode() : val(0), next(nullptr) {}
     *     ListNode(int x) : val(x), next(nullptr) {}
     *     ListNode(int x, ListNode *next) : val(x), next(next) {}
     * };
     */
    class Solution {
    public:
        ListNode* removeNthFromEnd(ListNode* head, int n) {
            ListNode *dummyHead = new ListNode(-1, head);
            ListNode *slow = dummyHead, *fast = dummyHead;
            while (n--) fast = fast->next; //fast先后移n个节点
            while (fast->next) { //slow和fast整体后移
                slow = slow->next;
                fast = fast->next;
            }
            ListNode *tmp = slow->next;
            slow->next = slow->next->next;
            delete tmp;
            head = dummyHead->next;
            delete dummyHead;
            return head;
        }
    };
    ```

#### 2.2、链表元素的交换

-   **解题方法**：双指针法等

    -   需要确定`pre`，`cur`指针的初始值
    -   `pre`，`cur`是如何更新的

-   **相关题目**：

    -   ##### [LC206.反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)

    ```c++
    /**
     * Definition for singly-linked list.
     * struct ListNode {
     *     int val;
     *     ListNode *next;
     *     ListNode() : val(0), next(nullptr) {}
     *     ListNode(int x) : val(x), next(nullptr) {}
     *     ListNode(int x, ListNode *next) : val(x), next(next) {}
     * };
     */
    class Solution {
    public:
        ListNode* reverseList(ListNode* head) {
            ListNode *dummyHead = new ListNode(-1);
            ListNode *pre = nullptr, *cur = head; //使用双指针
            while (cur) {
                ListNode *tmp = cur->next;
                cur->next = pre;
                pre = cur;
                cur = tmp;
            }
            return pre;
        }
    };
    ```

    ```c++
    /**
     * Definition for singly-linked list.
     * struct ListNode {
     *     int val;
     *     ListNode *next;
     *     ListNode() : val(0), next(nullptr) {}
     *     ListNode(int x) : val(x), next(nullptr) {}
     *     ListNode(int x, ListNode *next) : val(x), next(next) {}
     * };
     */
    class Solution {
    public:
        ListNode* reverseList(ListNode* head) {
            //另一种解法，借助虚拟头节点，把后面的节点依次出入虚拟头节点的后面
            ListNode *dummyHead = new ListNode(-1);
            ListNode *cur = head;
            while (cur) {
                ListNode *tmp = cur->next;
                cur->next = dummyHead->next;
                dummyHead->next = cur;
                cur = tmp;
            }
            return dummyHead->next;
        }
    };
    ```

    -   ##### [LC24.两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/submissions/)

        <img src="https://dongqing1903.oss-cn-shanghai.aliyuncs.com/img/image-20211103224928048.png" alt="image-20211103224928048" style="zoom:67%;" />

    ```c++
    /**
     * Definition for singly-linked list.
     * struct ListNode {
     *     int val;
     *     ListNode *next;
     *     ListNode() : val(0), next(nullptr) {}
     *     ListNode(int x) : val(x), next(nullptr) {}
     *     ListNode(int x, ListNode *next) : val(x), next(next) {}
     * };
     */
    class Solution {
    public:
        ListNode* swapPairs(ListNode* head) {
            ListNode *dummyHead = new ListNode(-1, head);
            ListNode *cur = dummyHead;
            while (cur->next && cur->next->next) {
                ListNode *tmp = cur->next, *tmp1 = cur->next->next->next;
                cur->next = cur->next->next;
                cur->next->next = tmp;
                cur->next->next->next = tmp1;
                cur = cur->next->next; //保证cur->next指向要处理的节点
            }
            return dummyHead->next;
        }
    };
    ```
    

#### 2.3、链表相关操作的实现  

-   **相关题目**：

    -   ##### [LC707.设计链表](https://leetcode-cn.com/problems/design-linked-list/)

    ```c++
    class MyLinkedList {
    private:
    //需要自己创建链表节点，使用单链表，节点只记录val、next
        struct ListNode {
            int val;
            ListNode *next;
            ListNode(int val) : val(val), next(nullptr) {}
        };
        //链表记录虚拟头节点、节点数量
        ListNode* dummyHead;
        int _size;
    public:
        MyLinkedList() { //链表的构造函数：初始化链表的内容
            dummyHead = new ListNode(-1);
            _size = 0;
        }
        
        int get(int index) { //获取指定位置节点的val值
            if (index >= _size || index < 0) return -1; //索引不存在的情乱
            ListNode *cur = dummyHead; //统一使用cur->next指向待处理的节点位置
            while (index--) cur = cur->next;
            return cur->next->val;
        }
        
        void addAtHead(int val) { //插入头节点
            ListNode *tmp = new ListNode(val); 
            tmp->next = dummyHead->next; 
            dummyHead->next = tmp;
            _size += 1; //勿忘
    
            return ;
        }
        
        void addAtTail(int val) { //插入尾节点
            ListNode *tmp = new ListNode(val);
            ListNode *cur = dummyHead;
            while (cur->next) cur = cur->next; //遍历到尾节点的位置
            cur->next = tmp;
            _size += 1; //勿忘
    
            return ;
        }
        
        void addAtIndex(int index, int val) { //在指定位置插入节点
            if (index > _size || index < 0) return ;
            ListNode *tmp = new ListNode(val);
            ListNode *cur = dummyHead;
            while (index--) cur = cur->next;
            tmp->next = cur->next;
            cur->next = tmp;
            _size += 1; //勿忘
    
            return ; 
        }
        
        void deleteAtIndex(int index) { //删除指定位置的节点
            if (index >= _size || index < 0) return ;
            ListNode *cur = dummyHead;
            while (index--) cur = cur->next;
            ListNode *tmp = cur->next;
            cur->next = cur->next->next;
            delete tmp; 
            _size -= 1; //勿忘
            
            return ;
        }
    };
    
    /**
     * Your MyLinkedList object will be instantiated and called as such:
     * MyLinkedList* obj = new MyLinkedList();
     * int param_1 = obj->get(index);
     * obj->addAtHead(val);
     * obj->addAtTail(val);
     * obj->addAtIndex(index,val);
     * obj->deleteAtIndex(index);
     */
    ```

#### 2.4、其他题型

-   ##### [LC面试题 02.07.链表相交](https://leetcode-cn.com/problems/intersection-of-two-linked-lists-lcci/)

    -   **解题思想**：
        
        ![image-20211104135625408](https://dongqing1903.oss-cn-shanghai.aliyuncs.com/img/image-20211104135625408.png)
        
    -   **代码演示**：
    
```c++
    /**
 * Definition for singly-linked list.
     * struct ListNode {
     *     int val;
     *     ListNode *next;
     *     ListNode(int x) : val(x), next(NULL) {}
     * };
     */
    class Solution {
    public:
        ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
            ListNode *a = headA, *b = headB;
            while (a != b) {
                a = a != nullptr ? a->next : headB;
                b = b != nullptr ? b->next : headA;
            }
            return a;
        }
    };
```

    -   **解题思想**：将链表A和链表B尾部对其，同时移动curA和curB，进行查找相同的位置
    
    ![image-20211104095242646](https://dongqing1903.oss-cn-shanghai.aliyuncs.com/img/image-20211104095242646.png)
    
    -   **代码演示**：
    
    ```c++
    /**
     * Definition for singly-linked list.
     * struct ListNode { 
     * 	int val;
     * 	ListNode *next;
     * 	ListNode(int x) : val(x), next(NULL) {}
     * };
     */
    class Solution {
        public:
        ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
            int cntA = 0, cntB = 0;
            ListNode *curA = headA, *curB = headB;
            while (curA) cntA += 1, curA = curA->next; //统计链表A的长度
            while (curB) cntB += 1, curB = curB->next; //统计链表B的长度
    
            curA = headA, curB = headB; //重置回指向头节点，或者重新定义两个指针
    
            if (cntA > cntB) { // 保证curA指向的是长度短的链表，curB指向的是长度长的链表
                swap(curA, curB);
                swap(cntA, cntB);
            }
            int gap = cntB - cntA; //两个链表的长度差
            while (gap--) curB = curB->next; //此时链表A,B尾部已经对其
            while (curA) {
                if (curA == curB) return curA;
                curA = curA->next;
                curB = curB->next;
            }
            return nullptr;
        }
    };
    ```

-   ##### [LC142.环形链表 II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

    -   **解题思想**：

        -   **判断链表是否有环**：使用快慢指针，**fast指针一定先进入环中，如果fast 指针和slow指针相遇的话，一定是在环中相遇。**

        -   **如果有环，找到这个环的入口**：

            假设从头结点到环形入口节点的节点数为`x`，环形入口节点到 `fast`指针与`slow`指针相遇节点的节点数为`y`， 从相遇节点再到环形入口节点节点数为`z`。 如图所示：

            ![image-20211104095631645](https://dongqing1903.oss-cn-shanghai.aliyuncs.com/img/image-20211104095631645.png)

            那么相遇时： `slow`指针走过的节点数为: `x + y`， `fast`指针走过的节点数：`x + y + n (y + z)`，`n`为`fast`指针在环内走了`n`圈才遇到`slow`指针，`(y+z)`为 一圈内节点的个数`A`。

            因为`fast`指针是一步走两个节点，`slow`指针一步走一个节点， 所以`fast`指针走过的节点数 = `slow`指针走过的节点数 `* 2`；

            因为要找环形的入口，那么要求的是`x`，`x`表示 头结点到环形入口节点的的距离；
            
            ![image-20211104112828853](https://dongqing1903.oss-cn-shanghai.aliyuncs.com/img/image-20211104112828853.png)
        
    -   **代码演示**：
    
    ```c++
    /**
     * Definition for singly-linked list.
     * struct ListNode {
     *     int val;
     *     ListNode *next;
     *     ListNode(int x) : val(x), next(NULL) {}
     * };
     */
    class Solution {
    public:
        ListNode *detectCycle(ListNode *head) {
            ListNode *fast = head, *slow = head;
            while (fast && fast->next) { //判断是否有环
                fast = fast->next->next; //快指针两倍速后移
                slow = slow->next; //慢指针一倍速后移
                if (fast == slow) break; //相遇退出，执行入口位置的确认
            } 
            if (fast == nullptr || fast->next == nullptr) return nullptr; //判断是否有环
            ListNode *index = head; //从头节点出发一个指针
            while (index != slow) { //从相遇点出发一个指针，两个指针一次移动一步
                slow = slow->next;
                index = index->next;
            }
            return index; //相遇之处即是入口        
        }
    };
    ```

#### 2.5、链表总结

-   **解题方法**：
    -   快慢指针法：
        -   相关题目：[LC142.环形链表 II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)，[LC206.反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)，[LC19.删除链表的倒数第 N 个结点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)
    -   普通遍历法：
        -   相关题目：[LC203.移除链表元素](https://leetcode-cn.com/problems/remove-linked-list-elements/)，[LC24.两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/submissions/)
    -   其他特殊处理：
        -   相关题目：[LC面试题 02.07.链表相交](https://leetcode-cn.com/problems/intersection-of-two-linked-lists-lcci/)

-   **解题技巧**：
    -   需要返回整个链表时，借助`虚拟头节点`
    -   涉及到前后节点之间的操作，需要定位到目标节点的前一个节点，使用`cur->next`指向目标节点
    -   对于`while`循环的条件，需要根据循环体中用到的`cur,cur->next,cur->next->next`的情况来确定

