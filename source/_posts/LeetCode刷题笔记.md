---
title: 算法刷题笔记
top: 253
tags:
  - 笔记
  - 技能
categories:
  - 笔记
  - 专业笔记
  - 算法刷题
abbrlink: 23889
date: 2021-10-28 08:31:37
comments: false
description: 本笔记主要记录LeetCode、HZOJ平台上的数组、链表、哈希表、字符串、双指针、栈与队列、二叉树、回溯算法、贪心算法、动态规划、单调栈、深搜广搜等主要分支以及面试笔试中常考的题型。
---
## 数组

### 1、数组的理论要点

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

-   **递推公式**：

    <img src="https://dongqing1903.oss-cn-shanghai.aliyuncs.com/img/image-20211121203040691.png" alt="image-20211121203040691" style="zoom:80%;" />

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

    -   ##### [LC3.无重复字符的最长子串

    ```c++
    class Solution {
    public:
        // 滑动窗口法
        int lengthOfLongestSubstring(string s) {
            int res = 0;
            unordered_set<char> uset;
            int l = 0, r = 0;
            for (int r = 0; r < s.size(); r++) {
                while (uset.find(s[r]) != uset.end() && l < r) {
                    uset.erase(s[l]);
                    l += 1;
                }
                uset.insert(s[r]);
                res = max(res, r - l + 1);
            }
            return res;
        }
    };
    ```
    
    ```c++
    class Solution {
    public:
        // 二分答案
        bool func(string &s, int len) {  // 验证s是否存在最长无重复的字符串长度为len
        char cnt[128] = {0};
            int k = 0;
        for (int i = 0; s[i]; i++) {
                cnt[s[i]] += 1;
                if (cnt[s[i]] == 1) k++;
                if (i >= len) {
                    cnt[s[i - len]] -= 1;
                    if (cnt[s[i - len]] == 0) k -= 1;
                }
                if (k == len) return true;
            }
            return false;
        }
        int lengthOfLongestSubstring(string s) {
            if (s.size() == 0) return 0;
            int l = 1, r = s.size();
            while (l < r) {
                int mid = (l + r + 1) >> 1;
                if (func(s, mid)) l = mid;
                else r = mid - 1; 
            }
            return l;
        }
    };
    ```
    
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
            int res = 0;
            int i = 0;
            unordered_map<int, int> umap; //记录果子种类
            for (int j = 0; j < fruits.size(); j++) {
                umap[fruits[j]] += 1;
                while (umap.size() > 2) {//窗口起始位置的移动处理
                    umap[fruits[i]] -= 1;
                    if (umap[fruits[i]] == 0) umap.erase(fruits[i]); //只有这种果子全清才可以
                    i += 1;
                }
                res = max(res, j - i + 1);
            }
            return res;
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
    
    >   **注**：移动窗口时，一定要使用`while`

#### 2.4、螺旋矩阵

-   **解题要点**：区间的循环不变量，本人采用[left, right)

-   **解题思路**：模拟顺时针画矩阵的过程

    1.  填充上行从左到右
    2.  填充右列从上到下
    3.  填充下行从右到左
    4.  填充左列从下到上

-   **相关题目**：

    -   ##### [LC59.螺旋矩阵 II :star::star::star:](https://leetcode-cn.com/problems/spiral-matrix-ii/)

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

### 1、链表的理论要点

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
    
    -   ##### [LC19.删除链表的倒数第N个结点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)
    
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

    -   ##### [LC206.翻转链表](https://leetcode-cn.com/problems/reverse-linked-list/)

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
    
-   ##### [LC24.两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs)
    
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

-   ##### [LC面试题02.07.链表相交](https://leetcode-cn.com/problems/intersection-of-two-linked-lists-lcci/)

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

-   ##### [LC142.环形链表II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

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

## 哈希表

### 1、哈希表的理论要点

-   哈希碰撞的解决方法：

    1.  拉链法
    2.  线性探测法

-   `C++`提供哈希表的结构：

    1.  数组
    2.  集合

    ![image-20211110191759224](https://dongqing1903.oss-cn-shanghai.aliyuncs.com/img/image-20211110191759224.png)

    3.  映射

    ![image-20211110191901490](https://dongqing1903.oss-cn-shanghai.aliyuncs.com/img/image-20211110191901490.png)

-   哈希表结构的选择：
    1.  数组 `>` 集合 `>` 映射
    2.  无序 `>` 有序

### 2、重点题型

-   #### [LC242.有效的字母异位词](https://leetcode-cn.com/problems/valid-anagram/)

    ```c++
    /*
     * 26个字母是确定数量，使用数组浪费不了多少空间，但是高效
     */
    class Solution {
    public:
        bool isAnagram(string s, string t) {
            int record[30] = {0};
            for (int i = 0; i < s.size(); i++) record[s[i] - 'a'] += 1;
            for (int i = 0; i < t.size(); i++) record[t[i] - 'a'] -= 1;
            for (int i = 0; i < 30; i++) 
                if (record[i] != 0) return false;
            return true;
        }
    };
    ```

-   #### [LC383.赎金信](https://leetcode-cn.com/problems/ransom-note/)

    ```c++
    class Solution {
    public:
        bool canConstruct(string ransomNote, string magazine) {
            int record[30] = {0};
            for (int i = 0; i < magazine.size(); i++) record[magazine[i] - 'a'] += 1;
            for (int i = 0; i < ransomNote.size(); i++) {
                record[ransomNote[i] - 'a'] -= 1;
                if (record[ransomNote[i] - 'a'] < 0) return false;
            }
            return true;
        }
    };
    ```

-   #### [LC49.字母异位词分组](https://leetcode-cn.com/problems/group-anagrams/)

    ```c++
    class Solution {
    public:
        vector<vector<string>> groupAnagrams(vector<string>& strs) {
            unordered_map<string, vector<string> > umap;
            vector<vector<string>> res;
            for (auto i : strs) {
                string tmp = i;
                sort(tmp.begin(), tmp.end());
                umap[tmp].push_back(i);
            }
            for (auto [s, v] : umap) {
                res.push_back(v);
            }
            return res;
        }
    };
    ```

-   #### [LC438.找到字符串中所有字母异位词](https://leetcode-cn.com/problems/find-all-anagrams-in-a-string/)

    ```c++
    class Solution {
    public:
        vector<int> findAnagrams(string s, string p) {
            int m = s.size(), n = p.size();
            vector<int> res;
            if (m < n) return res;
            vector<int> umap_p(26, 0), umap_s(26, 0);
            for (auto i : p) umap_p[i - 'a'] += 1;
            int slow = 0;
            for (int fast = 0; fast < s.size(); fast++) { //滑动窗口
                umap_s[s[fast] - 'a'] += 1;
                if (fast >= n) {
                    umap_s[s[slow] - 'a'] -= 1;
                    slow += 1;
                }
                if (umap_s == umap_p) res.push_back(slow);
            }
            return res;
        }
    };
    ```

-   #### [LC349.两个数组的交集](https://leetcode-cn.com/problems/intersection-of-two-arrays/)

    ```c++
    /*
     * 本题相当于求集合的交集，是不能有重复元素存在的，使用unordered_set集合即可
     */
    class Solution {
    public:
        vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
            unordered_set<int> uset, ures;
            vector<int> res;
            for (auto i : nums1) uset.insert(i);
            for (auto i : nums2) {
                if (uset.find(i) != uset.end()) ures.insert(i);
            }
            for (auto i : ures) res.push_back(i);
            return res;
        }
    };
    ```

-   #### [LC202.快乐数](https://leetcode-cn.com/problems/happy-number/)

    ```c++
    /*
     * 当出现循环时，sum会重复，可以使用哈希表标记sum值
     */
    class Solution {
    public:
        unordered_set<int> uset;
        int func(int n) {
            int sum = 0;
            while (n) {
                sum += (n % 10) * (n % 10);
                n /= 10;
            }
            return sum;
        }
        
        bool isHappy(int n) {
            int sum = n;
            while (1) {
                sum = func(sum);
                if (uset.find(sum) != uset.end()) return false;//陷入循环中
                uset.insert(sum);
                if (sum == 1) return true;
            }
        }
    };
    ```

-   #### [LC1.两数之和 :star::star::star:](https://leetcode-cn.com/problems/two-sum/)

    ```c++
    class Solution {
    public:
        vector<int> twoSum(vector<int>& nums, int target) {
            unordered_map<int, int> umap;
            for (int i = 0; i < nums.size(); i++) {
                if (umap.find(target - nums[i]) != umap.end()) return vector<int>{umap[target - nums[i]], i};
                umap[nums[i]] = i; //umap的值记录下标
            }
            return {};
        }
    };
    ```

    >   **注意**：与[LC15.三数之和](https://leetcode-cn.com/problems/3sum/)、[LC18.四数之和](https://leetcode-cn.com/problems/4sum/)的解法区别，三数、四数之和采用**双指针**更简单

-   #### [LC454.四数相加II :star::star::star:](https://leetcode-cn.com/problems/4sum-ii/)

    ```c++
    class Solution {
    public:
        int fourSumCount(vector<int>& nums1, vector<int>& nums2, vector<int>& nums3, vector<int>& nums4) {
            unordered_map<int, int> umap;
            for (auto i : nums1) {
                for (auto j : nums2) {
                    umap[i + j] += 1;
                }
            }
            int count = 0;
            for (auto i : nums3) {
                for (auto j : nums4) {
                    if (umap.find(0 - i - j) != umap.end()) {
                        count += umap[0 - i - j];
                    }
                }
            }
            return count;
        }
    };
    ```

### 3、哈希表总结

-   **数组作为哈希表**：
    1.  使用场景：元素数量是确定的，并且数量少，数组即可满足哈希要求
    2.  优点：数组在时间上是效率最高的
    3.  相关题目：[LC242.有效的字母异位词](https://leetcode-cn.com/problems/valid-anagram/)，[LC383.赎金信](https://leetcode-cn.com/problems/ransom-note/)
-   **集合`set`作为哈希表**：
    1.  使用场景：当使用数组时，哈希值比较分散，会造成空间的极大浪费，此时可以使用集合。优先选择使用底层是**哈希表**实现的`unordered_set`，其次如果需要排序时，使用底层是**红黑树**实现的`set`，如果不需要去重，使用底层是**红黑树**实现的`multiset`
    2.  相关题目：[LC349.两个数组的交集](https://leetcode-cn.com/problems/intersection-of-two-arrays/)，[LC202.快乐数](https://leetcode-cn.com/problems/happy-number/)

-   **映射`map`作为哈希表**：
    1.   使用场景：集合`set`只能记录`key`值，如果还需要记录`key`对应的`数值`构成**键值对**，就需要使用`map`。优先选择使用底层是**哈希表**实现的`unordered_map`，其次如果需要排序时，使用底层是**红黑树**实现的`map`，如果不需要去重，使用底层是**红黑树**实现的`multimap`
    2.  相关题目：[LC1.两数之和](https://leetcode-cn.com/problems/two-sum/)，[LC454.四数相加 II](https://leetcode-cn.com/problems/4sum-ii/)

## 字符串

### 1、字符串的理论要点

-   **C++中常用的字符串操作函数**：
    1.  返回子串`substr`(成员函数)：`对象.substr(pos, count)`
        -   `pos`：要包含的首个字符的位置
        -   `count`：子串的长度
        -   返回值：含子串`[pos, pos + count)`或者`[pos, pos + size()]`的string
    2.  更改存储的字符数`resize`：`对象.resize(count)`
        -   `count`：string的新大小
        -   无返回值
    3.  翻转字符串`reverse`：`reverse(s.begin(), s.begin() + count)`
        -   迭代器通用的操作
    4.  数值转换(非成员函数)
        -   `stoi,stol,stoll,stoul,stoull,stof,stod,stold`
        -   `to_string`

### 2、重点题型

-   #### [LC344.反转字符串](https://leetcode-cn.com/problems/reverse-string/)

    ```c++
    class Solution {
    public:
        void reverseString(vector<char>& s) {
            for (int i = 0, j = s.size() - 1; i < j; i++, j--) { //双指针法
                swap(s[i], s[j]);
            }
            return ;
        }
    };
    ```

-   #### [LC541.反转字符串 II](https://leetcode-cn.com/problems/reverse-string-ii/)

    ```c++
    class Solution {
    public:
        string reverseStr(string s, int k) {
            for (int i = 0; i < s.size(); i += (2 * k)) {
                // 每隔2K个字符的前K个字符进行反转
                // 剩余字符小于2K但大于或等于K个，则反转前K个字符
                if (i + k <= s.size()) {
                    reverse(s.begin() + i, s.begin() + i + k); //都是反转前K个字符
                    continue;
                }
                // 剩余字符小于K个字符，将剩余的字符全部反转
                reverse(s.begin() + i, s.begin() + s.size());
            }
            return s;
        }
    };
    ```

-   #### [LC剑指Offer05.替换空格](https://leetcode-cn.com/problems/ti-huan-kong-ge-lcof/)

    ```c++
    /*
     * 思路：先统计空格的数量，将字符串的长度扩展到填充%20后的长度，使用双指针从后往前遍历字符串，完成替换
     */
    class Solution {
    public:
        string replaceSpace(string s) {
            int count = 0;
            int oldSize = s.size();
            for (auto i : s) {
                if (i == ' ') count += 1; //统计空格数量
            }
            s.resize(oldSize + count * 2); //扩容
            int newSize = s.size();
            int j = oldSize - 1;
            for (int i = newSize - 1; i >= 0; i--, j--) { //从后往前遍历
                if (s[j] != ' ') s[i] = s[j];
                else {
                    s[i--] = '0';
                    s[i--] = '2';
                    s[i] = '%';
                }
            }
            return s;
        }
    };
    ```

-   #### [LC151.翻转字符串里的单词](https://leetcode-cn.com/problems/reverse-words-in-a-string/)

    ```c++
    /*
     * 思路：
     *     1、移除多余的空格
     * 	   2、将整个字符串反转
     *     3、将每个单词反转
     */
    
    class Solution {
    public:
        string reverseWords(string s) {
            // 1、移除多余的空格
            int pre = 0, cur = 0;
            // 1.1、移除开头的空格
            while (cur < s.size() && s[cur] == ' ') cur += 1; //cur指向开头不是空格的位置
            // 1.2、将内部的连续空格，替换成单个空格
            while (cur < s.size()) { 
                if (cur - 1 >= 0 && s[cur - 1] == ' ' && s[cur] == ' ') cur += 1;
                else {
                    s[pre] = s[cur];
                    pre += 1;
                    cur += 1;
                }         
            }
            // 1.3、移除尾部的空格，并重新定义字符串的大小
            if (pre - 1 > 0 && s[pre - 1] == ' ') s.resize(pre - 1);
            else s.resize(pre);
    		// 2、反转整个字符串
            reverse(s.begin(), s.end());
            // 3、将每个单词反转
            cur = 0, pre = 0;
            while (cur < s.size()) {
                while (cur < s.size() && s[cur] != ' ') cur += 1; //cur定位单词的结尾位置
                reverse(s.begin() + pre, s.begin() + cur);
                pre = cur + 1; //重新定位下一个单词的开始
                cur += 1;
            }
            return s;
        }
    };
    ```

-   #### [LC剑指Offer58 - II.左旋转字符串](https://leetcode-cn.com/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/)

    ```c++
    /*
     * 思路：
     *	1、反转前n个字符
     *  2、反转后 s.size() - n 个字符
     *  3、反转整个字符串
     */
    class Solution {
    public:
        string reverseLeftWords(string s, int n) {
            int len = s.size();
            if (n == len) return s;
            n %= len;
            reverse(s.begin(), s.begin() + n); // 1
            reverse(s.begin() + n, s.end()); // 2
            reverse(s.begin(), s.end()); // 3
            return s;
        }
    };
    ```

    ```c++
    /*
     * 使用字符串拼接，空间复杂度变大
     */
    class Solution {
    public:
        string reverseLeftWords(string s, int n) {
            int len = s.size();
            if (n == len) return s;
            n %= len;
            string str1 = s.substr(0, n); 
            string str2 = s.substr(n, len - n);
            str2 += str1;
            return str2;
        }
    };
    ```

-   #### [LC28.实现 strStr()](https://leetcode-cn.com/problems/implement-strstr/)

    ```c++
    // KMP算法
    class Solution {
    public:
        void getNext(int *next, string &t) {
            next[0] = 0;
            for (int j = 0, i = 1; t[i]; i++) {
                while (j != 0 && t[j] != t[i]) j = next[j - 1];
                if (t[i] == t[j]) j += 1;
                next[i] = j;
            }
            return ;
        }
        int strStr(string s, string t) {
            if (t.size() == 0) return 0; 
            int next[t.size()];
            getNext(next, t);
    
            for (int i  = 0, j = 0; s[i]; i++) {
                while (j != 0 && s[i] != t[j]) j = next[j - 1];
                if (s[i] == t[j]) j += 1;
                if (t[j] == 0) return i - j + 1;
            }
            return -1;
        }
    };
    ```

-   #### [LC459.重复的子字符串](https://leetcode-cn.com/problems/repeated-substring-pattern/)

    ```c++
    // KMP算法，只需求next数组
    class Solution {
public:
        void getNext(int *next, string &s) {
            next[0] = 0;
            for (int j = 0, i = 1; s[i]; i++) {
                while (j != 0 && s[j] != s[i]) j = next[j - 1];
                if (s[i] == s[j]) j += 1;
                next[i] = j;
            }
            return ;
        }
        bool repeatedSubstringPattern(string s) {
            int next[s.size()];
            getNext(next, s);
            int len = s.size();
            if (next[len - 1] != 0 && len % (len - next[len - 1]) == 0) return true;
            return false;
        }
    };
    ```

### 3、字符串总结

-   双指针法：[LC344.反转字符串](https://leetcode-cn.com/problems/reverse-string/)，[LC剑指Offer05.替换空格](https://leetcode-cn.com/problems/ti-huan-kong-ge-lcof/)，[LC151.翻转字符串里的单词](https://leetcode-cn.com/problems/reverse-words-in-a-string/)

-   字符串匹配(KMP算法)：[LC28.实现 strStr()](https://leetcode-cn.com/problems/implement-strstr/)，[LC459.重复的子字符串](https://leetcode-cn.com/problems/repeated-substring-pattern/)

## 双指针

### 1、重点题型

-   #### [LC27.移除元素](https://leetcode-cn.com/problems/remove-element/)

-   #### [LC344.反转字符串](https://leetcode-cn.com/problems/reverse-string/)

-   #### [LC剑指Offer05.替换空格](https://leetcode-cn.com/problems/ti-huan-kong-ge-lcof/)

-   #### [LC206.翻转链表](https://leetcode-cn.com/problems/reverse-linked-list/)

-   #### [LC19.删除链表的倒数第N个结点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

-   #### [LC面试题02.07.链表相交](https://leetcode-cn.com/problems/intersection-of-two-linked-lists-lcci/)

-   #### [LC142.环形链表 II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

-   #### [LC2.两数相加](https://leetcode-cn.com/problems/add-two-numbers/)

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
        ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
            ListNode *head = new ListNode(), *tail = head;
            int sum = 0;
            while (l1 || l2 || sum) {
                int n1 = l1 ? l1->val : 0;
                int n2 = l2 ? l2->val : 0;
                l1 = l1 ? l1->next : nullptr;
                l2 = l2 ? l2->next : nullptr;
                sum += n1 + n2;
                tail->next = new ListNode(sum % 10);
                tail = tail->next;
                sum /= 10;
            }
            return head->next;
        }
    };
    ```

    

-   #### [LC15.三数之和](https://leetcode-cn.com/problems/3sum/)

    ```c++
    class Solution {
    public:
        vector<vector<int>> threeSum(vector<int>& nums) {
            int size = nums.size();
            vector<vector<int>> res;
            // 进行排序
            sort(nums.begin(), nums.end()); 
            // 对于排序好的序列，如果第一个元素大于0，后面两个元素之和必然大于0，都不满足情况
            for (int i = 0; i < size && nums[i] <= 0; i++) { 
                // 第一个元素去重，不能使用nums[i] == nums[i + 1]去重，那样会过度去重 -1 -1 2
                if (i > 0 && nums[i] == nums[i - 1]) continue;
                // 使用双指针，寻找后面满足条件的两个元素
                int fast = size - 1, slow = i + 1;
                int sum = 0 - nums[i];
                while (fast > slow) {
                    if (nums[fast] + nums[slow] > sum) fast -= 1;
                    else if (nums[fast] + nums[slow] < sum) slow += 1;
                    else {
                        res.push_back(vector<int>{nums[i], nums[slow], nums[fast]});
                        //第2个元素去重
                        while (fast > slow && nums[fast] == nums[fast - 1]) fast -= 1;
                        //第3个元素去重
                        while (fast > slow && nums[slow] == nums[slow + 1]) slow += 1;
                        slow += 1;
                        fast -= 1;
                    }
                }
            }
            return res;
        }
    };
    ```

-   #### [LC18.四数之和](https://leetcode-cn.com/problems/4sum/)

    ```c++
    class Solution {
    public:
        vector<vector<int>> fourSum(vector<int>& nums, int target) {
            int size = nums.size();
            vector<vector<int>> res;
            sort(nums.begin(), nums.end());
            // 此处不能通过 nums[i] <= target 进行剪枝
            // 如果 target < 0, 是不可以的剪枝的, target = -5, 那么 -3 + -2 + 0 + 0 = -5
            // 如果 target >= 0, 是可以剪枝的
            for (int i = 0; i < size - 3; i++) {
                // 第一个元素去重
                if (i > 0 && nums[i] == nums[i - 1]) continue;
                // 此处同样不能剪枝
                for (int j = i + 1; j < size - 2; j++) {
                    // 第二个元素去重
                    if (j > i + 1 && nums[j] == nums[j - 1]) continue;
                    int fast = size - 1, slow = j + 1;
                    int sum = target - nums[i] - nums[j];
                    while (slow < fast) {
                        if (nums[fast] + nums[slow] > sum) fast -= 1;
                        else if (nums[fast] + nums[slow] < sum) slow += 1;
                        else {
                            res.push_back(vector<int>{nums[i], nums[j], nums[slow], nums[fast]});
                            // 第三、四个元素去重
                            while (slow < fast && nums[fast] == nums[fast - 1]) fast -= 1;
                            while (slow < fast && nums[slow] == nums[slow + 1]) slow += 1;
                            slow += 1;
                            fast -= 1; 
                        }
                    }
                }
            }
            return res;
        }
    };
    ```

### 2、双指针总结

-   同一数组中查找`N`(**N > 2**)数之和问题，前面`N - 2`个数通过**for循环**进行遍历，后面`2`个数通过双指针确定，使用双指针处理N数之和问题，便于去重，而哈希表不适合去重，注意区分[LC1.两数之和](https://leetcode-cn.com/problems/two-sum/)，[LC454.四数相加 II](https://leetcode-cn.com/problems/4sum-ii/)，[LC15.三数之和](https://leetcode-cn.com/problems/3sum/)，[LC18.四数之和](https://leetcode-cn.com/problems/4sum/)的解法。

## 栈与队列

### 1、栈与队列的理论要点



### 2、重点题型

-   #### [LC232.用栈实现队列](https://leetcode-cn.com/problems/implement-queue-using-stacks/)

    ```c++
    // 元素的插入直接插入到staPush栈中
    // 元素的弹出从staPop栈弹出
    class MyQueue {
    public:
        MyQueue() {
    
        }
        
        void push(int x) { // 元素的插入直接插入到staPush中
            staPush.push(x);
        }
        
        int pop() { // 元素的弹出从staPop栈中弹出
            // 如果staPop栈为空，将staPush栈中的元素全部插入到staPop栈中
            if (staPop.empty()) {
                while (!staPush.empty()) {
                    staPop.push(staPush.top());
                    staPush.pop();
                }
            }
            // staPop栈不为空，直接弹出staPop栈的栈首元素
            int temp = staPop.top();
            staPop.pop();
            return temp;
        }
        
        int peek() { // 借助pop操作，实现peek操作
            int temp = this->pop();
            staPop.push(temp);
            return temp;
        }
        
        bool empty() {
            return staPop.empty() && staPush.empty();
        }
    private:
        stack<int> staPush, staPop;
    };
    
    /**
     * Your MyQueue object will be instantiated and called as such:
     * MyQueue* obj = new MyQueue();
     * obj->push(x);
     * int param_2 = obj->pop();
     * int param_3 = obj->peek();
     * bool param_4 = obj->empty();
     */
    ```

-   #### [LC225.用队列实现栈](https://leetcode-cn.com/problems/implement-stack-using-queues/)

    ```c++
    // 使用另外一个que2队列用来周转
    class MyStack {
    public:
        MyStack() {
    
        }
        
        void push(int x) { // que1队列用来插入元素
            que1.push(x);
        }
        
        int pop() { 
            // 将que1队列中弹出压入que2中，只剩下最后一个元素
            while (que1.size() > 1) {
                que2.push(que1.front());
                que1.pop();
            }
            // 弹出最后一个元素
            int temp = que1.front();
            que1.pop();
            // 再将弹出的元素压入队列中
            while (!que2.empty()) {
                que1.push(que2.front());
                que2.pop();
            }
            return temp;
        }
        
        int top() { // 栈顶元素就是que1的队尾元素
            return que1.back();
        }
        
        bool empty() {
            return que1.empty();
        }
    private:
        queue<int> que1, que2;
    };
    
    /**
     * Your MyStack object will be instantiated and called as such:
     * MyStack* obj = new MyStack();
     * obj->push(x);
     * int param_2 = obj->pop();
     * int param_3 = obj->top();
     * bool param_4 = obj->empty();
     */
    ```

    ```c++
    class MyStack {
    private:
        queue<int> que; // 使用一个队列模拟实现
    public:
        MyStack() {
    
        }
        
        void push(int x) {
            que.push(x);
            return ;
        }
        
        int pop() {
            int size = que.size() - 1;
            while (size--) {
                que.push(que.front());
                que.pop();
            }
            int temp = que.front();
            que.pop();
            return temp;
        }
        
        int top() {
            return que.back();
        }
        
        bool empty() {
            return que.empty();
        }
    };
    
    /**
     * Your MyStack object will be instantiated and called as such:
     * MyStack* obj = new MyStack();
     * obj->push(x);
     * int param_2 = obj->pop();
     * int param_3 = obj->top();
     * bool param_4 = obj->empty();
     */
    ```

-   #### [LC20.有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)

    ```c++
    class Solution {
    public:
        bool isValid(string s) {
            stack<char> sta;
            for (auto i : s) {
                if (i == '(') sta.push(')') ;
                else if (i == '{') sta.push('}'); 
                else if (i == '[') sta.push(']');
                else if (!sta.empty() && sta.top() == i) {
                    sta.pop();
                } else {
                    return false;
                }
            }
            return sta.empty();
        }
    };
    ```

-   #### [LC1047.删除字符串中的所有相邻重复项](https://leetcode-cn.com/problems/remove-all-adjacent-duplicates-in-string/)

    ```c++
    class Solution {
    public:
        string removeDuplicates(string s) {
            stack<char> sta;
            for (auto i : s) {
                if (!sta.empty() && sta.top() == i) {
                    sta.pop();
                    continue;
                }
                sta.push(i);
            }
            string str = "";
            while (!sta.empty()) {
                str += sta.top();
                sta.pop();
            }
            reverse(str.begin(), str.end());
            return str;
        }
    };
    ```

    ```c++
    class Solution {
    public:
        string removeDuplicates(string s) {
            string str = ""; // 使用string模拟栈操作
            for (auto i : s) {
                if (str.size() > 0 && str.back() == i) {
                    str.pop_back();
                    continue;
                } 
                str.push_back(i);
            }
            return str;
        }
    };
    ```

-   #### [LC150.逆波兰表达式求值](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)

    ```c++
    class Solution {
    public:
        int evalRPN(vector<string>& tokens) {
            stack<int> sta;
            for (auto i : tokens) {
                if (i == "+" || i == "-" || i == "*" ||i == "/") {
                    int s1 = sta.top();
                    sta.pop();
                    int s2 = sta.top();
                    sta.pop();
                    int sum;
                    if (i == "+") sum = s1 + s2;
                    else if (i == "-")  sum = s2 - s1;
                    else if (i == "*") sum = s1 * s2;
                    else if ( i == "/") sum = s2 / s1;
                    sta.push(sum); // 将结果压入栈
                } else {
                    sta.push(stoi(i));
                }
            }
            return sta.top();
        }
    };
    ```

-   #### [LC239.滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/)

    ```c++
    // 单调队列：队首元素保存当前的最大值
    class Solution {
    private:
        class Myque { // 实现单调队列
        private:
            deque<int> deq; // 需要在队尾、队首删除元素，使用双端队列
        public:
            void pop(int val) {  // 当队首元素需要移出窗口时
                if (!deq.empty() && deq.front() == val) deq.pop_front();
            }
            void push(int val) {
                while (!deq.empty() && deq.back() < val) { // 可以保证插入后，队列元素的顺序和原顺序一致
                    deq.pop_back();
                }
                deq.push_back(val);     
            }
            int front() {
                return deq.front();
            }
        };
    public:
        vector<int> maxSlidingWindow(vector<int>& nums, int k) {
            Myque que;
            vector<int> result;
            for (int i = 0; i < k; i++) {
                que.push(nums[i]);
            }
            result.push_back(que.front());
            for (int i = k; i < nums.size(); i++) {
                que.pop(nums[i - k]);
                que.push(nums[i]);
                result.push_back(que.front());
            }
            return result;
        }
    };
    ```

-   #### [LC347.前 K 个高频元素](https://leetcode-cn.com/problems/top-k-frequent-elements/)

    ```c++
    class Solution {
    public:
        class mycompare {
        public:
            bool operator()(const pair<int, int> &a, const pair<int, int> &b) {
                return a.second > b.second;
            }
        };
        vector<int> topKFrequent(vector<int>& nums, int k) {
            unordered_map<int, int> umap;
            for (auto i : nums) umap[i] += 1;
            priority_queue<pair<int, int>, vector<pair<int, int>>, mycompare> que;
            for (auto &[fi, se] : umap) {
                que.push(pair<int, int>{fi, se});
                if (que.size() > k) {
                    que.pop();
                } 
            }
            vector<int> res;
            while (!que.empty()) {
                res.push_back(que.top().first);
                que.pop();
            }
            return res;
        }
    };
    ```

## 二叉树

### 1、二叉树的理论要点

-   **递归算法的三要素**：
    1.  **确定递归函数的参数和返回值：** 确定哪些参数是递归的过程中需要处理的，那么就在递归函数里加上这个参数， 并且还要明确每次递归的返回值是什么进而确定递归函数的返回类型。
    2.  **确定终止条件：** 写完了递归算法, 运行的时候，经常会遇到栈溢出的错误，就是没写终止条件或者终止条件写的不对，操作系统也是用一个栈的结构来保存每一层递归的信息，如果递归没有终止，操作系统的内存栈必然就会溢出。
    3.  **确定单层递归的逻辑：** 确定每一层递归需要处理的信息。在这里也就会重复调用自己来实现递归的过程。
    
-   **递归函数的返回值，递归函数什么时候需要返回值？什么时候不需要返回值？总结如下三点**：
1.  如果需要**搜索整颗二叉树且不用处理递归返回值**，递归函数就不要返回值。
    
2.  如果需要搜索整颗二叉树且需要**处理递归返回值**，递归函数就需要返回值。 
    
3.  如果要**搜索其中一条符合条件的路径**，那么递归一定需要返回值，因为遇到符合条件的路径了就要及时返回。
    
-   **一般情况来说：如果让空节点（空指针）进入递归，就不加if，如果不让空节点进入递归，就加if限制一下， 终止条件也会相应的调整。**

### 2、重点题型

-   #### [LC144.二叉树的前序遍历](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        /* 
         * 递归法:
         * 分析：递归法三要素
    	 * 1、确定递归函数的参数和返回值：因为每一次遍历都要记录下节点的值，需要使用vector来存储，其次还需要传入要遍历的节点；不需要有返回值，返回值类型为void
    	 * 2、确认终止条件：当前遍历节点为空就返回
    	 * 3、单层递归的逻辑：前序遍历顺序为中左右，先记录当前节点的值，再递归遍历左子树和右子树的值
    	 * 综上：void traversal(TreeNode *root, vector<int> &res);  
    	 * 函数类型和给出的函数类型不同，需要单独定义
    	 */
        void traversal(TreeNode *root, vector<int> &res) {
            if (root == nullptr) return ; // 终止条件
            res.push_back(root->val); // 中
            traversal(root->left, res); // 左，递归遍历左子树
            traversal(root->right, res); // 右，递归遍历左子树
            return ;
        }
        vector<int> preorderTraversal(TreeNode* root) {
            vector<int> res;
            traversal(root, res);
            return res;
        }
    };
    ```

    ```c++
    // 迭代法
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        vector<int> preorderTraversal(TreeNode* root) {
            /*
             * 递归遍历二叉树的左右子树只能是栈来存储树的节点，同时按照遍历顺序逆序放入栈中，前序遍历需要先放右节点再放左节点，弹出时就可以先遍历左子树，后遍历右子树
             * 使用迭代方法需要处理两个过程：
             * 1、处理：将元素放进res数组中
             * 2、访问：遍历节点
             * 前序遍历的顺序和存放元素的顺序是一致的，先访问中间节点再处理中间节点，不需要额外变量辅助
             */
            stack<TreeNode *> sta; 
            vector<int> res;
            if (root == nullptr) return res;
            sta.push(root);
            while (!sta.empty()) {
                TreeNode *cur = sta.top();
                sta.pop();
                res.push_back(cur->val); // 中
                // 只记录非空节点
                if (cur->right) sta.push(cur->right); // 右
                if (cur->left) sta.push(cur->left); // 左
            }
            return res;
        }
    };
    ```

-   #### [LC145.二叉树的后序遍历](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        void traversal(TreeNode *root, vector<int> &res) {
            if (root == nullptr) return ; // 终止条件
            traversal(root->left, res); // 左：递归遍历左子树
            res.push_back(root->val); // 中
            traversal(root->right, res); // 右：递归遍历右子树
            return ;
        }
        vector<int> inorderTraversal(TreeNode* root) {
            vector<int> res;
            traversal(root, res);
            return res;
        }
    };
    ```

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        vector<int> postorderTraversal(TreeNode* root) {
            /*
             * 后续遍历是在前序遍历的基础上改造即可，按照中右左的顺序遍历，将得到的结果翻转即可
             */
            vector<int> res;
            stack<TreeNode *> sta;
            if (root == nullptr) return res;
            sta.push(root);
            while (!sta.empty()) {
                TreeNode *cur = sta.top();
                sta.pop();
                res.push_back(cur->val); // 中
                if (cur->left) sta.push(cur->left); // 左
                if (cur->right) sta.push(cur->right); // 右
            }
            reverse(res.begin(), res.end());
            return res;
        }
    };
    ```

-   #### [LC589.N 叉树的前序遍历](https://leetcode-cn.com/problems/n-ary-tree-preorder-traversal/)

    ```c++
    /*
    // Definition for a Node.
    class Node {
    public:
        int val;
        vector<Node*> children;
    
        Node() {}
    
        Node(int _val) {
            val = _val;
        }
    
        Node(int _val, vector<Node*> _children) {
            val = _val;
            children = _children;
        }
    };
    */
    
    class Solution {
    public:
        void traversal(Node *root, vector<int> &res) {
            if (root == nullptr) return ;
            res.push_back(root->val); // 中
            for (auto i : root->children) traversal(i, res);
            return ;
        }
        vector<int> preorder(Node* root) {
            vector<int> res;
            traversal(root, res);
            return res;
        }
    };
    ```

    ```c++
    /*
    // Definition for a Node.
    class Node {
    public:
        int val;
        vector<Node*> children;
    
        Node() {}
    
        Node(int _val) {
            val = _val;
        }
    
        Node(int _val, vector<Node*> _children) {
            val = _val;
            children = _children;
        }
    };
    */
    
    class Solution {
    public:
        vector<int> preorder(Node* root) {
            vector<int> res;
            stack<Node*> sta;
            if (root == nullptr) return res;
            sta.push(root);
            while (!sta.empty()) {
                Node *cur = sta.top();
                sta.pop();
                res.push_back(cur->val);
                for (int i = cur->children.size() - 1; i >= 0; i--) { // 逆序插入栈中，最左边的需要最先取出
                    if (cur->children[i] != nullptr) sta.push(cur->children[i]);
                }
            }
            return res;
        }
    };
    ```

-   #### [LC590.N 叉树的后序遍历](https://leetcode-cn.com/problems/n-ary-tree-postorder-traversal/)

    ```c++
    /*
    // Definition for a Node.
    class Node {
    public:
        int val;
        vector<Node*> children;
    
        Node() {}
    
        Node(int _val) {
            val = _val;
        }
    
        Node(int _val, vector<Node*> _children) {
            val = _val;
            children = _children;
        }
    };
    */
    
    class Solution {
    public:
        void traversal(Node *root, vector<int> &res) {
            if (root == nullptr) return ;
            for (auto i : root->children) traversal(i, res);
            res.push_back(root->val);
            return ;
        }
        vector<int> postorder(Node* root) {
            vector<int> res;
            traversal(root, res);
            return res;
        }
    };
    ```

    ```c++
    /*
    // Definition for a Node.
    class Node {
    public:
        int val;
        vector<Node*> children;
    
        Node() {}
    
        Node(int _val) {
            val = _val;
        }
    
        Node(int _val, vector<Node*> _children) {
            val = _val;
            children = _children;
        }
    };
    */
    
    class Solution {
    public:
        vector<int> postorder(Node* root) {
            vector<int> res;
            stack<Node*> sta;
            if (root == nullptr) return res;
            sta.push(root);
            while (!sta.empty()) {
                Node *cur = sta.top();
                sta.pop();
                res.push_back(cur->val);
                for (auto i : cur->children) sta.push(i); // 和前序遍历相反的顺序
            }
            reverse(res.begin(), res.end());
            return res;
        }
    };
    ```

-   #### [LC94.二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        void traversal(TreeNode *root, vector<int> &res) {
            if (root == nullptr) return ; // 终止条件
            traversal(root->left, res); // 左：递归遍历左子树
            res.push_back(root->val); // 右：递归遍历右子树
            traversal(root->right, res); // 中
            return ;
        }
        vector<int> inorderTraversal(TreeNode* root) {
            vector<int> res;
            traversal(root, res);
            return res;
        }
    };
    ```

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        vector<int> inorderTraversal(TreeNode* root) {
            /*
             * 使用迭代方法需要处理两个过程：
             * 1、处理：将元素放进res数组中
             * 2、访问：遍历节点
             * 中序遍历的顺序和存放元素的顺序是不一致的，先访问最左节点再处理节点，需要额外变量辅助
             */
            vector<int> res;
            stack<TreeNode *> sta;
            if (root == nullptr) return res;
            TreeNode *cur = root;
            while (cur != nullptr || !sta.empty()) {
                if (cur != nullptr) {
                    sta.push(cur);
                    cur = cur->left;
                } else {
                    cur = sta.top();
                    sta.pop();
                    res.push_back(cur->val);
                    cur = cur->right;
                }
            }
            return res;
        } 
    };
    ```

-   #### [LC102.二叉树的层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        vector<vector<int>> levelOrder(TreeNode* root) {
            /*
             * 层序队列使用队列进行节点保存
             */
            queue<TreeNode *> que;
            vector<vector<int>> res;
            if (root == nullptr) return res;
            que.push(root);
            while (!que.empty()) {
                int size = que.size();
                vector<int> vec;
                for (int i = 0; i < size; i++) {
                    TreeNode *cur = que.front();
                    que.pop();
                    vec.push_back(cur->val);
                    if (cur->left) que.push(cur->left);
                    if (cur->right) que.push(cur->right);
                }
                res.push_back(vec);
            }
            return res;
        }
    };
    ```

-   #### [LC107.二叉树的层序遍历 II](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        vector<vector<int>> levelOrderBottom(TreeNode* root) {
            queue<TreeNode *> que;
            vector<vector<int>> res;
            if (root == nullptr) return res;
            que.push(root);
            while (!que.empty()) {
                int size = que.size();
                vector<int> vec;
                for (int i = 0; i < size; i++) {
                    TreeNode *cur = que.front();
                    que.pop();
                    vec.push_back(cur->val);
                    if (cur->left) que.push(cur->left);
                    if (cur->right) que.push(cur->right);
                }
                res.push_back(vec);
            }
            reverse(res.begin(), res.end());
            return res;
        }
    };
    ```

-   #### [LC199.二叉树的右视图](https://leetcode-cn.com/problems/binary-tree-right-side-view/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        vector<int> rightSideView(TreeNode* root) {
            queue<TreeNode *> que;
            vector<int> res;
            if (root == nullptr) return res;
            que.push(root);
            while (!que.empty()) {
                int size = que.size();
                for (int i = 0; i < size; i++) {
                    TreeNode *cur = que.front();
                    que.pop();
                    if (i == size - 1) res.push_back(cur->val);
                    if (cur->left) que.push(cur->left);
                    if (cur->right) que.push(cur->right);
                }
            }
            return res;
        }
    };
    ```

-   #### [LC637.二叉树的层平均值](https://leetcode-cn.com/problems/average-of-levels-in-binary-tree/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        vector<double> averageOfLevels(TreeNode* root) {
            vector<double> res;
            queue<TreeNode *> que;
            if (root == nullptr) return res;
            que.push(root);
            while (!que.empty()) {
                int size = que.size();
                double sum = 0; // 定义成double，方便后面做除法
                for (int i = 0; i < size; i++) {
                    TreeNode *cur = que.front();
                    que.pop();
                    sum += cur->val;
                    if (cur->left) que.push(cur->left);
                    if (cur->right) que.push(cur->right);
                }
                res.push_back(sum / size);
            }
            return res;
        }
    };
    ```

-   #### [LC515.在每个树行中找最大值](https://leetcode-cn.com/problems/find-largest-value-in-each-tree-row/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        vector<int> largestValues(TreeNode* root) {
            queue<TreeNode *> que;
            vector<int> res;
            if (root == nullptr) return res;
            que.push(root);
            while (!que.empty()) {
                int size = que.size();
                int temp = INT_MIN;
                for (int i = 0; i < size; i++) {
                    TreeNode *cur = que.front();
                    que.pop();
                    temp = max(temp, cur->val);
                    if (cur->left) que.push(cur->left);
                    if (cur->right) que.push(cur->right);
                }
                res.push_back(temp);
            }
            return res;
        }
    
    };
    ```

-   #### [LC116.填充每个节点的下一个右侧节点指针](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/)

    ```c++
    /*
    // Definition for a Node.
    class Node {
    public:
        int val;
        Node* left;
        Node* right;
        Node* next;
    
        Node() : val(0), left(NULL), right(NULL), next(NULL) {}
    
        Node(int _val) : val(_val), left(NULL), right(NULL), next(NULL) {}
    
        Node(int _val, Node* _left, Node* _right, Node* _next)
            : val(_val), left(_left), right(_right), next(_next) {}
    };
    */
    
    class Solution {
    public:
        Node* connect(Node* root) {
            queue<Node*> que;
            if (root == nullptr) return root;
            que.push(root);
            while (!que.empty()) {
                int size = que.size();
                for (int i = 0; i < size; i++) {
                    Node *cur = que.front();
                    que.pop();
                    if (i != size - 1) cur->next = que.front();
                    if (cur->left) que.push(cur->left);
                    if (cur->right) que.push(cur->right);
                }
            }
            return root;
        }
    };
    ```

-   #### [LC117.填充每个节点的下一个右侧节点指针 II](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node-ii/)

    ```c++
    /*
    // Definition for a Node.
    class Node {
    public:
        int val;
        Node* left;
        Node* right;
        Node* next;
    
        Node() : val(0), left(NULL), right(NULL), next(NULL) {}
    
        Node(int _val) : val(_val), left(NULL), right(NULL), next(NULL) {}
    
        Node(int _val, Node* _left, Node* _right, Node* _next)
            : val(_val), left(_left), right(_right), next(_next) {}
    };
    */
    
    class Solution {
    public:
        Node* connect(Node* root) {
           queue<Node*> que;
            if (root == nullptr) return root;
            que.push(root);
            while (!que.empty()) {
                int size = que.size();
                for (int i = 0; i < size; i++) {
                    Node *cur = que.front();
                    que.pop();
                    if (i != size - 1) cur->next = que.front();
                    else cur->next = nullptr;
                    if (cur->left) que.push(cur->left);
                    if (cur->right) que.push(cur->right);
                }
            }
            return root;
        }
    };
    ```

-   #### [LC226.翻转二叉树](https://leetcode-cn.com/problems/invert-binary-tree/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        TreeNode* invertTree(TreeNode* root) {
            if (root == nullptr) return root;
            swap(root->left, root->right); // 中
            root->left = invertTree(root->left); // 左
            root->right = invertTree(root->right); // 右
            return root;
        }
    };
    ```

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        TreeNode* invertTree(TreeNode* root) {
            stack<TreeNode*> sta;
            if (root == nullptr) return root;
            sta.push(root);
            while (!sta.empty()) {
                TreeNode *cur = sta.top();
                sta.pop();
                swap(cur->left, cur->right);
                if (cur->left) sta.push(cur->left);
                if (cur->right) sta.push(cur->right);
            }
            return root;
        }
    };
    ```
    
    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        TreeNode* invertTree(TreeNode* root) {
            /* 每一个节点都要翻转它的左右子树，使用栈或者队列都可以，需要把所有节点走一遍*/
            queue<TreeNode*> que;
            if (root == nullptr) return root;
            que.push(root);
            while (!que.empty()) {
                int size = que.size();
                for (int i = 0; i < size; i++) {
                    TreeNode *cur = que.front();
                    que.pop();
                    swap(cur->left, cur->right);
                    if (cur->left) que.push(cur->left);
                    if (cur->right) que.push(cur->right);
                }
            }
            return root;
        }
    };
    ```
    
-   #### [LC101.对称二叉树](https://leetcode-cn.com/problems/symmetric-tree/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        bool symmetric(TreeNode *left, TreeNode *right) {
            if (left == nullptr && right == nullptr) return true; // 终止条件
            if (left == nullptr || right == nullptr) return false;
            if (left->val != right->val) return false;
            return symmetric(left->left, right->right) && symmetric(left->right, right->left);  
        }
        bool isSymmetric(TreeNode* root) {
            if (root == nullptr) return true;
            return symmetric(root->left, root->right);
        }
    };
    ```

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        bool isSymmetric(TreeNode* root) {
            /*
             * 迭代法使用栈或者队列都可以，只要是成对取元素即可
             * 使用队列：是按照层遍历进行比较
             * 使用栈：是按照树的深度，沿着一棵树递归遍历比较，但最后所有的对称点都可以遍历到
             */
            if (root == nullptr) return false;
            queue<TreeNode *> que;
            que.push(root->right);
            que.push(root->left);
            while (!que.empty()) {
                TreeNode *left = que.front();
                que.pop();
                TreeNode *right = que.front();
                que.pop();
                
                if (left == nullptr && right == nullptr) continue;
                if (left == nullptr || right == nullptr) return false;
                if (left->val != right->val) return false;
                
                que.push(right->right); // 注意顺序
                que.push(left->left);
                que.push(right->left);
                que.push(left->right);
            }
            return true;
        }
    };
    ```

-   #### [LC100.相同的树](https://leetcode-cn.com/problems/same-tree/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        bool isSameTree(TreeNode* p, TreeNode* q) {
            if (p == nullptr && q == nullptr) return true;
            if (p == nullptr || q == nullptr) return false;
            if (p->val != q->val) return false;
            return isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
        }
    };
    ```

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        bool isSameTree(TreeNode* p, TreeNode* q) {
            queue<TreeNode *> que;
            que.push(p);
            que.push(q);
            while (!que.empty()) {
                TreeNode *left = que.front(); 
                que.pop();
                TreeNode *right = que.front();
                que.pop();
                if (left == nullptr && right == nullptr) continue;
                if (left == nullptr || right == nullptr) return false;
                if (left->val != right->val) return false;
                que.push(left->left);
                que.push(right->left);
                que.push(left->right);
                que.push(right->right);
            }
            return true;
        }
    };
    ```

-   #### [LC104.二叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        int maxDepth(TreeNode* root) {
            /*
             * 求最大深度 == 求树的根节点的高度
             * 求高度是从底层往高层计算，需要通过后序遍历的顺序求解，当前节点的高度 = 1 + max(左节点, 右节点)
             */
            if (root == nullptr) return 0; // 终止条件
            return 1 + max(maxDepth(root->left), maxDepth(root->right)); 
        }
    };
    ```

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        /*
         * 直接求每个节点的深度，比较得出最大深度
         * 求深度需要从上层往下层遍历，采用前序遍历的顺序
         * 采用回溯的思想，对于递归函数中的参数depth是来指定当前节点的深度，如果求左节点的深度改变了，对于同一层的右节点的深度也被改变了
         */
        int result = 0;
        void getDepth(TreeNode *node, int depth) {
            if (node == nullptr) return ; // 终止条件
            result = max(result, depth); // 中
            if (node->left) getDepth(node->left, depth + 1); // 左
            if (node->right) getDepth(node->right, depth + 1); // 右
            return ;
        }
        int maxDepth(TreeNode* root) {
            if (root == nullptr) return result;
            getDepth(root, 1);
            return result;
        }
    };
    ```

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        int maxDepth(TreeNode* root) {
            /*
             * 层序遍历
             */
            queue<TreeNode *> que;
            int count = 0;
            if (root == nullptr) return count;
            que.push(root);
            while (!que.empty()) {
                int size = que.size();
                count += 1;
                for (int i = 0; i < size; i++) {
                    TreeNode *cur = que.front();
                    que.pop();
                    if (cur->left) que.push(cur->left);
                    if (cur->right) que.push(cur->right);
                }
            }
            return count;
        }
    };
    ```

-   #### [LC559.N叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-n-ary-tree/)

    ```c++
    /*
    // Definition for a Node.
    class Node {
    public:
        int val;
        vector<Node*> children;
    
        Node() {}
    
        Node(int _val) {
            val = _val;
        }
    
        Node(int _val, vector<Node*> _children) {
            val = _val;
            children = _children;
        }
    };
    */
    
    class Solution {
    public:
        int maxDepth(Node* root) {
            if (root == nullptr) return 0;
            int height = 0; // 此处不能使用全局变量，否则没调用一个节点的递归函数height就会递增1
            for (auto i : root->children) {
                height = max(height, maxDepth(i)); // 左右
            }
            return height + 1; // 中
        }
    };
    ```

    ```c++
    /*
    // Definition for a Node.
    class Node {
    public:
        int val;
        vector<Node*> children;
    
        Node() {}
    
        Node(int _val) {
            val = _val;
        }
    
        Node(int _val, vector<Node*> _children) {
            val = _val;
            children = _children;
        }
    };
    */
    
    class Solution {
    public:
        int res = 0;
        void getDepth(Node *root, int depth) {
            if (root == nullptr) return ;
            res = max(res, depth + 1);
            for (auto i : root->children) {
                getDepth(i, depth + 1);
            }
            return ;
        }
    
        int maxDepth(Node* root) {
            getDepth(root, 0);
            return res;
        }
    };
    ```

    ```c++
    /*
    // Definition for a Node.
    class Node {
    public:
        int val;
        vector<Node*> children;
    
        Node() {}
    
        Node(int _val) {
            val = _val;
        }
    
        Node(int _val, vector<Node*> _children) {
            val = _val;
            children = _children;
        }
    };
    */
    
    class Solution {
    public:
        vector<vector<int>> levelOrder(Node* root) {
            /*
             * 层序遍历
             */
            queue<Node*> que;
            vector<vector<int>> res;
            if (root == nullptr) return res;
            que.push(root);
            while (!que.empty()) {
                int size = que.size();
                vector<int> vec;
                for (int i = 0; i < size; i++) {
                    Node *cur = que.front();
                    que.pop();
                    vec.push_back(cur->val);
                    for (auto i : cur->children) que.push(i); 
                }
                res.push_back(vec);
            }
            return res;
        }
    };
    ```

-   #### [LC111.二叉树的最小深度](https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        int minDepth(TreeNode* root) {
            /*
             * 画图帮助理解
             * 按照高度求最小深度，和普通二叉树高度不同，此题说的高度是相对于每个叶子节点来计算的高度，在每一层选举高度最小的节点作为最小深度的节点
             * 同样按照后序遍历的顺序
             */
            if (root == nullptr) return 0;
            if (root->left == nullptr && root->right == nullptr) return 1;
            if (root->left == nullptr) return 1 + minDepth(root->right);
            if (root->left != nullptr) return 1 + minDepth(root->left);
            return 1 + min(minDepth(root->left), minDepth(root->right));
        }
    };
    ```

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        int minDepth(TreeNode* root) {
            /*
             * 层序遍历
             */
            queue<TreeNode*> que;
            int count = 0;
            if (root == 0) return count;
            que.push(root);
            while (!que.empty()) {
                int size = que.size();
                count += 1;
                for (int i = 0; i < size; i++) {
                    TreeNode *cur = que.front();
                    que.pop();
                    if (cur->left == nullptr && cur->right == nullptr) return count; // 最小深度处
                    if (cur->left) que.push(cur->left);
                    if (cur->right) que.push(cur->right);
                }
            }
            return count;
        }
    };
    ```

-   #### [LC222.完全二叉树的节点个数](https://leetcode-cn.com/problems/count-complete-tree-nodes/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        int countNodes(TreeNode* root) {
            /*
             * 递归算法采用后序遍历的顺序，当前节点所包含的节点数 = 左子树节点数目 + 右子树节点数目 + 1，需要先遍历左右子树，再遍历中间节点
             */
            if (root == nullptr) return 0;
            return countNodes(root->left) + countNodes(root->right) + 1; // 左，右，中
        }
    };
    ```

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        int countNodes(TreeNode* root) {
            /*
             * 层序遍历
             */
            if (root == nullptr) return 0;
            queue<TreeNode *> que;
            que.push(root);
            int result = 0;
            while (!que.empty()) {
                int size = que.size();
                for (int i = 0; i < size; i++) {
                    TreeNode *cur = que.front();
                    que.pop();
                    result += 1;
                    if (cur->left) que.push(cur->left);
                    if (cur->right) que.push(cur->right);
                }
            }
            return result;
        }
    };
    ```

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        int countNodes(TreeNode* root) {
            /*
             * 将完全二叉树分解为完美二叉树，通过完美二叉树节点的计算方式求完全二叉树的节点数目
             */
            if (root == nullptr) return 0;
            
            TreeNode *left = root->left;
            TreeNode *right = root->right;
            int leftHeight = 0, rightHeight = 0;
            while (left) { // 求左子树最左边的高度
                left = left->left;
                leftHeight += 1;
            }
            while (right) { // 求右子树最右边的高度
                right = right->right;
                rightHeight += 1;
            }
            if (leftHeight == rightHeight) return (2 << leftHeight) - 1; // 此时说明是完美二叉树
            return countNodes(root->left) + countNodes(root->right) + 1; // 将完全二叉树划分为完美二叉树
        }
    };
    ```

-   #### [LC110.平衡二叉树](https://leetcode-cn.com/problems/balanced-binary-tree/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        int getHeight(TreeNode *root) {
            if (root == nullptr) return 0;
            int leftHeight = getHeight(root->left);
            if (leftHeight == -1) return -1;
            int rightHeight = getHeight(root->right);
            if (rightHeight == -1) return -1;
            return abs(leftHeight - rightHeight) > 1 ? -1 : max(leftHeight, rightHeight) + 1;
        }
        bool isBalanced(TreeNode* root) {
            return getHeight(root) == -1 ? false : true;
        }
    };
    ```

-   #### [LC257.二叉树的所有路径](https://leetcode-cn.com/problems/binary-tree-paths/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        /*
         * 递归 + 回溯
         */
        void traversal(TreeNode *cur, string path, vector<string> &res) { // 此处不能是string &，string &不能绑定表达式上
            path += to_string(cur->val);
            if (cur->left == nullptr && cur->right == nullptr) {
                res.push_back(path);
                return ;
            }
            if (cur->left) traversal(cur->left, path + "->", res); // 回溯思想，保证cur->right的遍历还是从path开始
            if (cur->right) traversal(cur->right, path + "->", res);
            return ;
        }
        vector<string> binaryTreePaths(TreeNode* root) {
            vector<string> res;
            string path;
            traversal(root, path, res);
            return res;
        }
    };
    ```

-   #### [LC404.左叶子之和](https://leetcode-cn.com/problems/sum-of-left-leaves/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        int sumOfLeftLeaves(TreeNode* root) {
            // 采用前序或后续遍历都可以
            if (root == nullptr) return 0;
            // 左叶子节点的判断
            if (root->left != nullptr && root->left->left == nullptr && root->left->right == nullptr) return root->left->val + sumOfLeftLeaves(root->right); // 如果当前节点左节点是叶子节点，只需要再递归遍历右子树查找左叶子节点即可
            return sumOfLeftLeaves(root->left) + sumOfLeftLeaves(root->right); // 左右节点都不是叶子节点，就递归遍历查找
        }
    };
    ```

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        int sumOfLeftLeaves(TreeNode* root) {
            /*
             * 迭代法
             * 按照前序遍历的顺序，如果是左叶子节点就加入结果中
             */
            int res = 0;
            if (root == nullptr) return res;
            stack<TreeNode *> sta;
            sta.push(root);
            while (!sta.empty()) {
                TreeNode *cur = sta.top();
                sta.pop();
                if (cur->left != nullptr && cur->left->left == nullptr && cur->left->right == nullptr) res += cur->left->val;
                if (cur->right) sta.push(cur->right);
                if (cur->left) sta.push(cur->left);
            }
            return res;
        }
    };
    ```

-   #### [LC513.找树左下角的值](https://leetcode-cn.com/problems/find-bottom-left-tree-value/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        /*
         * 递归算法
         * 通过附加参数当前节点的深度来确定最底层的最左边的叶子节点
         */
        int maxLen = INT_MIN;
        int maxleftVal;
        void traversal(TreeNode *root, int leftLen) {
            // 终止条件
            if (root->left == nullptr && root->right == nullptr) { 
                if (leftLen > maxLen) { // 当遇到更深节点，由于先遍历左子树，后遍历右子树，可以保证在同一层中记录的是最左边的节点
                    maxLen = leftLen;
                    maxleftVal = root->val;
                }
                return ;
            }
            // 遍历顺序不能变，需要先遍历左子树，如果是题目是选择最右边的节点，需要先遍历右子树
            if (root->left) traversal(root->left, leftLen + 1); // 左
            if (root->right) traversal(root->right, leftLen + 1); // 右
            return ; 
        }
        int findBottomLeftValue(TreeNode* root) {
            traversal(root, 1);
            return maxleftVal;
        }
    };
    ```

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        int findBottomLeftValue(TreeNode* root) {
            /*
             * 迭代法
             */
            if (root == nullptr) return 0;
            queue<TreeNode*> que;
            que.push(root);
            int res = 0;
            while (!que.empty()) {
                int size = que.size();
                for (int i = 0; i < size; i++) {
                    TreeNode *cur = que.front();
                    que.pop();
                    if (i == 0) res = cur->val; // 不断的更新，直到最后一层
                    if (cur->left) que.push(cur->left);
                    if (cur->right) que.push(cur->right);
                }
            }
            return res;
        }
    };
    ```

-   #### [LC112.路径总和](https://leetcode-cn.com/problems/path-sum/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        /*
         * 递归法
         */
        bool hasPathSum(TreeNode* root, int targetSum) {
            if (root == nullptr) return false;
            /*
             * 1、计数通过递减的方式传递
             * 2、叶子节点对应的计数值达到要求就终止，此时已找到符合要求的路径
             */
            if (root->left == nullptr && root->right == nullptr && targetSum == root->val) return true;
            // 
            return hasPathSum(root->left, targetSum - root->val) || hasPathSum(root->right, targetSum - root->val); // 还有回溯的思想
        }
    };
    ```

-   #### [LC113.路径总和 II](https://leetcode-cn.com/problems/path-sum-ii/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        vector<vector<int>> res;
        vector<int> path;
        void traversal(TreeNode* root, int targetSum) {
            if (root == nullptr) return ;
            /*
             * 不能以root == nullptr为条件, 要不然遍历root->left会产生一次path，遍历root->right产生一次path
             */
            if (root->left == nullptr && root->right == nullptr && targetSum == root->val) {
                path.push_back(root->val);
                res.push_back(path);
                path.pop_back(); // 由于path用的全局变量，下一轮回溯中不应该有root->val，要不然使用局部变量传参进来
                return ;
            }
            path.push_back(root->val);
            if (root->left) traversal(root->left, targetSum - root->val);
            if (root->right) traversal(root->right, targetSum - root->val);
            path.pop_back();
            return ;
        }
        vector<vector<int>> pathSum(TreeNode* root, int targetSum) {
            traversal(root, targetSum);
            return res;
        }
    };
    ```
    
-   #### [LC106.从中序与后序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        TreeNode* traversal(vector<int>& inorder, int inorderLeft, int inorderRight, vector<int>& postorder, int postorderLeft, int postorderRight) {
            if (inorderLeft >= inorderRight) return nullptr;
            int midVal = postorder[postorderRight - 1];
            TreeNode *root = new TreeNode(midVal);
            int midIndex = inorderLeft;
            while (inorder[midIndex] != midVal) midIndex += 1;
            root->left = traversal(inorder, inorderLeft, midIndex, postorder, postorderLeft, postorderLeft + midIndex - inorderLeft);
            root->right = traversal(inorder, midIndex + 1, inorderRight, postorder, postorderLeft + midIndex - inorderLeft, postorderRight - 1); // 注意位置的变换
            return root;
        }
        TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
            if (inorder.size() == 0) return nullptr;
            return traversal(inorder, 0, inorder.size(), postorder, 0, postorder.size());
        }
    };
    ```

-   #### [LC105.从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        // 递归构造树都需要有返回值
        TreeNode *traversal(vector<int>& preorder, int preorderLeft, int preorderRight, vector<int> &inorder, int inorderLeft, int inorderRight) {
            if (preorderRight - preorderLeft == 0) return nullptr;
            int midVal = preorder[preorderLeft];
            TreeNode *root = new TreeNode(midVal);
            int midIndex = inorderLeft;
            while (inorder[midIndex] != midVal) midIndex += 1;
            root->left =  traversal(preorder, preorderLeft + 1, preorderLeft + midIndex - inorderLeft + 1, inorder, inorderLeft, midIndex);
            root->right = traversal(preorder, preorderLeft + midIndex - inorderLeft + 1, preorderRight, inorder, midIndex + 1, inorderRight);
            return root;
        }
        TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
            if (preorder.size() == 0) return nullptr;
            return traversal(preorder, 0, preorder.size(), inorder, 0, inorder.size());
        }
    };
    ```

-   #### [LC654.最大二叉树](https://leetcode-cn.com/problems/maximum-binary-tree/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        
        TreeNode *traversal(vector<int>& nums, int left, int right) {
            if (left == right) return nullptr;
            int maxIndex = left; // 
            for (int i = left; i < right; i++) {
                if (nums[maxIndex] < nums[i]) maxIndex = i;
            }
            TreeNode *root = new TreeNode(nums[maxIndex]);
            root->left = traversal(nums, left, maxIndex);
            root->right = traversal(nums, maxIndex + 1, right);
            return root;
        }
        TreeNode* constructMaximumBinaryTree(vector<int>& nums) {
            if (nums.size() == 0) return nullptr;
            return traversal(nums, 0, nums.size());
        }
    };
    ```

-   #### [LC617.合并二叉树](https://leetcode-cn.com/problems/merge-two-binary-trees)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        TreeNode* mergeTrees(TreeNode* root1, TreeNode* root2) {
            if (root1 == nullptr) return root2;
            if (root2 == nullptr) return root1;
            root1->val += root2->val;
            root1->left = mergeTrees(root1->left, root2->left);
            root1->right = mergeTrees(root1->right, root2->right);
            return root1;
        }
    };
    ```
    
    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
        public:
        TreeNode* mergeTrees(TreeNode* root1, TreeNode* root2) {
            queue<TreeNode*> que;
            if (root1 == nullptr) return root2;
            if (root2 == nullptr) return root1;
            que.push(root1);
            que.push(root2);
            while (!que.empty()) {
                TreeNode *leftNode = que.front();
                que.pop();
                TreeNode *rightNode = que.front();
                que.pop();
    
                leftNode->val += rightNode->val;
                if (leftNode->left && rightNode->left) {
                    que.push(leftNode->left);
                    que.push(rightNode->left);
                }
                if (leftNode->right && rightNode->right) {
                    que.push(leftNode->right);
                    que.push(rightNode->right);
                }
    
                if (leftNode->left == nullptr && rightNode->left != nullptr) leftNode->left = rightNode->left;
                if (leftNode->right == nullptr && rightNode->right != nullptr) leftNode->right = rightNode->right; 
            }
            return root1;
        }
    };
    ```
-   #### [LC700.二叉搜索树中的搜索](https://leetcode-cn.com/problems/search-in-a-binary-search-tree/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        TreeNode* searchBST(TreeNode* root, int val) {
            if (root == nullptr) return nullptr;
            if (root->val == val) return root;
            if (root->val < val) return searchBST(root->right, val);
            return searchBST(root->left, val);
        }
    };
    ```

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        TreeNode* searchBST(TreeNode* root, int val) {
            while (root) {
                if (root->val == val) return root;
                if (root->val < val) root = root->right;
                else root = root->left;
            }
            return root;
        }
    };
    ```

-   #### [LC98.验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        long long pre = LONG_MIN;
        bool isValidBST(TreeNode* root) { //中序遍历
            if (root == nullptr) return true;
            bool leftNode = isValidBST(root->left);
            if (pre >= root->val) return false;
            pre = root->val;
            bool rightNode = isValidBST(root->right);
            return leftNode && rightNode;
        }
    };
    ```

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        bool isValidBST(TreeNode* root) { // 中序遍历
            stack<TreeNode*> sta;
            TreeNode *cur = root;
            TreeNode *pre = nullptr;
            while (cur != nullptr || !sta.empty()) {
                if (cur != nullptr) {
                    sta.push(cur);
                    cur = cur->left;
                } else {
                    cur = sta.top();
                    sta.pop();
                    if (pre != nullptr && cur->val <= pre->val) return false;
                    pre = cur;
                    cur = cur->right;
                }
            }
            return true;
        }
    };
    ```

-   #### [LC530.二叉搜索树的最小绝对差](https://leetcode-cn.com/problems/minimum-absolute-difference-in-bst/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        TreeNode *pre = nullptr;
        int minVal = INT_MAX;
        void traversal(TreeNode* root) {
            if (root == nullptr) return ;
            traversal(root->left);
            if (pre != nullptr) minVal = min(minVal, root->val - pre->val);
            pre = root;
            traversal(root->right);
            return ;
        }
        int getMinimumDifference(TreeNode* root) {
            traversal(root);
            return minVal;
        }
    };
    ```

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        int getMinimumDifference(TreeNode* root) {
            stack<TreeNode *> sta;
            TreeNode *cur = root;
            TreeNode *pre = nullptr;
            int res = INT_MAX;
            while (cur != nullptr || !sta.empty()) {
                if (cur != nullptr) {
                    sta.push(cur);
                    cur = cur->left;
                } else {
                    cur = sta.top();
                    sta.pop();
                    if (pre != nullptr) res = min(res, cur->val - pre->val);
                    pre = cur;
                    cur = cur->right;
                }
            }
            return res;
        }
    };
    ```

-   #### [LC501.二叉搜索树中的众数](https://leetcode-cn.com/problems/find-mode-in-binary-search-tree/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        vector<int> res;
        int count = 0;
        int maxCount = 0;
        TreeNode *pre = nullptr;
        void traversal(TreeNode* root) {
            if (root == nullptr) return ;
            traversal(root->left);
            if (pre == nullptr) count = 1;
            else if (pre->val == root->val) count += 1;
            else count = 1;
            if (count == maxCount) res.push_back(root->val);
            if (count > maxCount) {
                maxCount = count;
                res.clear(); // res永远保存的是当前遍历过的众数
                res.push_back(root->val);
            } 
            pre = root;
            traversal(root->right);
            return ;
        }
        vector<int> findMode(TreeNode* root) {
            traversal(root);
            return res;
        }
    };
    ```

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        vector<int> findMode(TreeNode* root) {
            vector<int> res;
            int count = 0, maxCount = 0;
            stack<TreeNode *> sta;
            TreeNode *cur = root;
            TreeNode *pre = nullptr;
            while (cur != nullptr || !sta.empty()) {
                if (cur != nullptr) {
                    sta.push(cur);
                    cur = cur->left;
                } else {
                    cur = sta.top();
                    sta.pop();
                    if (pre == nullptr) count = 1;
                    else if (pre->val == cur->val) count += 1;
                    else count = 1;
                    if (count == maxCount) res.push_back(cur->val);
                    if (count > maxCount) {
                        maxCount = count;
                        res.clear();
                        res.push_back(cur->val);
                    }
                    pre = cur;
                    cur = cur->right;
                }  
            }
            return res;
        }
    };
    ```

-   #### [LC236.二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
     * };
     */
    class Solution {
    public:
        /*
         * 求公共祖先需要从底往上遍历，使用后续遍历
         * 此题需要遍历整棵树，因为需要根据left和right做判断
         */
        TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
            if (root == p || root == q || root == nullptr) return root; // 如果先遍历到了p，那么q要么在p的子树上，要么在别的树上，反之一样
            TreeNode *left = lowestCommonAncestor(root->left, p, q);
            TreeNode *right = lowestCommonAncestor(root->right, p, q);
            // 此时left和right的值只有两种可能：nullptr 或 p,q所在的节点
            // 将目标逐层上传
            if (left != nullptr && right != nullptr) return root; // root是p,q的父节点
            // right是目标，用目标覆盖当前节点，继续上传
            if (left == nullptr) return right; // left = nullptr right != nullptr 此时right一定为p或q
            return left; // 同理此时left一定是p或q， 或者left right都为nullptr
        }
    };
    ```

-   #### [LC235.二叉搜索树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
     * };
     */
    
    class Solution {
    public:
        TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
            if (root == nullptr) return nullptr;
            if (p->val > root->val && q->val > root->val) return lowestCommonAncestor(root->right, p, q);
            if (p->val < root->val && q->val < root->val) return lowestCommonAncestor(root->left, p, q);
            return root;
        }
    };
    ```

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
     * };
     */
    
    class Solution {
    public:
        TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
            while (root) {
                if (root->val < p->val && root->val < q->val) root = root->right;
                else if (root->val > p->val && root->val > q->val) root = root->left;
                else return root;
            }
            return nullptr;
        }
    };
    ```

-   #### [LC701.二叉搜索树中的插入操作](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        TreeNode* insertIntoBST(TreeNode* root, int val) {
            if (root == nullptr) return new TreeNode(val);
            if (root->val > val) root->left = insertIntoBST(root->left, val);
            if (root->val < val) root->right = insertIntoBST(root->right, val);
            return root;
        }
    };
    ```

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        TreeNode* insertIntoBST(TreeNode* root, int val) {
            if (root == nullptr) return new TreeNode(val);
            TreeNode *cur = root, *pre = root;
            while (cur) {
                pre = cur;
                if (cur->val > val) cur = cur->left;
                else cur = cur->right;
            }
            if (val < pre->val) pre->left = new TreeNode(val);
            else pre->right = new TreeNode(val);
            return root;
        }
    
    };
    ```

-   #### [LC450.删除二叉搜索树中的节点](https://leetcode-cn.com/problems/delete-node-in-a-bst/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        TreeNode* preccedeNode(TreeNode* root) {
            while (root->right) root = root->right;
            return root;
        } 
        TreeNode* deleteNode(TreeNode* root, int key) {
            if (root == nullptr) return root;
            if (root->val > key) root->left = deleteNode(root->left, key);
            else if (root->val < key) root->right = deleteNode(root->right, key);
            else {
                // 叶子节点或只有一个子节点的节点
                if (root->left == nullptr || root->right == nullptr) {
                    TreeNode *temp = root->left ? root->left : root->right;
                    delete root;
                    return temp;
                }
                else { // 含有两个子节点的节点
                    TreeNode *temp = preccedeNode(root->left);
                    root->val = temp->val;
                    root->left = deleteNode(root->left, temp->val);
                }
            }
            return root;
        }
    };
    ```

-   #### [LC669. 修剪二叉搜索树](https://leetcode-cn.com/problems/trim-a-binary-search-tree/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        TreeNode* trimBST(TreeNode* root, int low, int high) {
            if (root == nullptr) return root;
            if (root->val < low) return trimBST(root->right, low, high);
            if (root->val > high) return trimBST(root->left, low, high);
            root->left = trimBST(root->left, low, high);
            root->right = trimBST(root->right, low, high);
            return root;
        }
    };
    ```

-   #### [LC108.将有序数组转换为二叉搜索树](https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        TreeNode* traversal(vector<int>& nums, int left, int right) {
            if (left == right) return nullptr;
            int midIndex = left + (right - left) / 2;  
            TreeNode *root = new TreeNode(nums[midIndex]);
            root->left = traversal(nums, left, midIndex);
            root->right = traversal(nums, midIndex + 1, right);
            return root;
        }
        TreeNode* sortedArrayToBST(vector<int>& nums) {
            return traversal(nums, 0, nums.size());
        }
    };
    ```

-   #### [LC538.把二叉搜索树转换为累加树](https://leetcode-cn.com/problems/convert-bst-to-greater-tree/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        /*
         * 右中左的遍历顺序
         */
        TreeNode *pre = nullptr;
        void traversal(TreeNode* root) {
            if (root == nullptr) return ;
            traversal(root->right);
            if (pre != nullptr) {
                root->val += pre->val;
            }
            pre = root;
            traversal(root->left);
            return ;
        }
        TreeNode* convertBST(TreeNode* root) {
            traversal(root);
            return root;
        }
    };
    ```

### 3、二叉树总结

-   **二叉搜索树中涉及到跟前后值的，借用`pre`指向前一个节点**
-   **求高度实际是按照层序遍历从底往上求**
-   **递归只需要确定函数的参数和返回值以及终止条件，其他的对于二叉树来说基本都是操作左、中、右三个节点，分析清楚操作顺序，直接写即可**
-   **递归中的参数可以通过全局变量来代替也可以**
-   使用迭代法同时处理两棵树时，把两个树的节点`同时`加入队列进行比较

## 回溯算法

### 1、回溯算法的理论要点

-   何时需要`startIndex`
    -   如果是一个集合求组合的话，需要`strtIndex`
    -   如果是多个集合去组合，各个集合之间相互不影响，不需要`startIndex`
-   `for`是树层操作，递归是树枝操作



### 2、重点题型

-   #### [LC77.组合](https://leetcode-cn.com/problems/combinations/)

    ```c++
    class Solution {
    public:
        vector<vector<int>> res;
        vector<int> path;
        // 要把n, k传进来，同时使用startIndex来标记起始位置
        void backtracing(int n, int k, int startIndex) {
            if (path.size() == k) {
                res.push_back(path);
                return ;
            }
            for (int i = startIndex; i <= n - (k - path.size()) + 1; i++) { // 剪枝操作，保证能够取满K个数
                path.push_back(i); 
                backtracing(n, k, i + 1); // 纵向递归
                path.pop_back();
            }
        }
        vector<vector<int>> combine(int n, int k) {
            backtracing(n, k, 1);
            return res;
        }
    };
    ```

-   #### [LC216.组合总和 III](https://leetcode-cn.com/problems/combination-sum-iii/)

    ```c++
    class Solution {
    public:
        vector<vector<int>> res;
        vector<int> path;
    
        void backtracing(int k, int n, int sum, int startIndex) {
            if (path.size() == k) {
                if (sum == n) res.push_back(path);
                return ;
            }
            for (int i = startIndex; i <= 9 - (k - path.size()) + 1; i++) { // 剪枝操作
                path.push_back(i);
                backtracing(k, n, sum + i, i + 1);
                path.pop_back();
            }
        }
        vector<vector<int>> combinationSum3(int k, int n) {
            backtracing(k, n, 0, 1);
            return res;
        }
    };
    ```

-   #### [LC17.电话号码的字母组合](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)

    ```c++
    class Solution {
    public:
        const string letterMap[10] = { "", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
        string path; //
        vector<string> res;
        void backtracing(const string&digits, int index) { // index标记访问到digits的第几个字符
            if (index == digits.size()) {
                res.push_back(path);
                return ;
            }
            int digit = digits[index] - '0'; // 表示第index个字符代表的数字
            string letters = letterMap[digit];
            for (int i = 0; i < letters.size(); i++) { // 横向遍历这一层
                path.push_back(letters[i]);
                backtracing(digits, index + 1); 
                path.pop_back();
            }
        } 
        vector<string> letterCombinations(string digits) {
            if (digits.size() == 0) return res;
            backtracing(digits, 0);
            return res;
        }
    };
    ```

-   #### [LC39.组合总和](https://leetcode-cn.com/problems/combination-sum/)

    ```c++
    class Solution {
    public:
        vector<vector<int>> res;
        vector<int> path;
        void backtracing(vector<int>& candidates, int target, int startIndex) {
            if (target < 0) return ;
            if (target == 0) {
                res.push_back(path);
                return ;
            }
            for (int i = startIndex; i < candidates.size(); i++) {
                path.push_back(candidates[i]);
                backtracing(candidates, target - candidates[i], i); // 起始位置不变，可以重复使用该元素
                path.pop_back(); 
            }
            return ;
        }
        vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
            backtracing(candidates, target, 0);
            return res;
        }
    };
    ```

-   #### [LC40.组合总和 II](https://leetcode-cn.com/problems/combination-sum-ii/)

    ```c++
    class Solution {
    public:
        /*
         * 根据题意知，同一个组合中是可以重复的，但两个组合不能相同
         * 把回溯过程抽象成一棵树，允许树枝重复，但不允许树层重复，对树层去重
         * 对树层去重需要对数组排序
         * used[i] == true 表示同一树枝使用过
         * used[i] == false 表示同一树层使用过
         */
        vector<vector<int>> res;
        vector<int> path;
        bool used[105];
        void backtracing(vector<int>& candidates, int target, int startIndex) {
            if (target < 0) return ;
            if (target == 0) {
                res.push_back(path);
                return ;
            }
            for (int i = startIndex; i < candidates.size(); i++) {
                if (i > 0 && candidates[i - 1] == candidates[i] && used[i - 1] == false) continue; // 对于同一树层之前有使用过的，本层直接退出
                path.push_back(candidates[i]);
                used[i] = true; // 告知本树枝后面的元素可以重复该元素
                backtracing(candidates, target - candidates[i], i + 1);
                used[i] = false;
                path.pop_back();
            }
            return ;
        }
        vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
            sort(candidates.begin(), candidates.end());
            backtracing(candidates, target, 0);
            return res;
        }
    };
    ```

-   #### [LC131.分割回文串](https://leetcode-cn.com/problems/palindrome-partitioning/)

    ```c++
    class Solution {
    public:
        vector<vector<string>> res;
        vector<string> path;
        bool isPartion(const string& s, int l, int r) { // 判断回文串
            for (int i = l, j = r; i < j; i++, j--) {
                if (s[i] != s[j]) return false;
            }
            return true;
        }
        /*
         * 切割问题就是组合问题
         * 字符串的分隔符 == startIndex
         * 当startIndex >= s.size(), 表示切割线已到最后，结束递归
         */
        void traversal(const string& s, int startIndex) { 
            if (startIndex >= s.size()) { // 终止条件
                res.push_back(path);
                return ;
            }
            for (int i = startIndex; i < s.size(); i++) { 
                if (isPartion(s, startIndex, i)) path.push_back(s.substr(startIndex, i - startIndex + 1)); 
                else continue;
                traversal(s, i + 1); // 起始位置后移，保证不重复
                path.pop_back();
            }
            return ;
        }
        vector<vector<string>> partition(string s) {
            traversal(s, 0);
            return res;
        }
    };
    ```

-   #### [LC93.复原 IP 地址](https://leetcode-cn.com/problems/restore-ip-addresses/)

    ```c++
    class Solution {
    public:
        vector<string> res;
        void backtracing(string s, int startIndex, int pointNum) {
            if (pointNum == 3) { // 必须以.为判断依据
                if (isValid(s, startIndex, s.size() - 1)) res.push_back(s); // 判断第4段是否正确
                return ;
            }
            for (int i = startIndex; i < s.size(); i++) {
                if (isValid(s, startIndex, i)) {
                    s.insert(s.begin() + i + 1, '.');
                    backtracing(s, i + 2, pointNum + 1); // 由于插入.，下一个startIndex需要从i+2开始
                    s.erase(s.begin() + i + 1); // 回溯
                } else break;
            }
            return ;
        }
    
        bool isValid(const string& s, int l, int r) { 
            if (l > r) return false;
            if (s[l] == '0' && l != r) return false;
            long long num = 0;
            for (int i = l; i <= r; i++) {
                if (s[i] < '0' || s[i] > '9') return false;
                num = num * 10 + (s[i] - '0');
            }
            return num > 255 ? false : true;
        }
        vector<string> restoreIpAddresses(string s) {
            backtracing(s, 0, 0);
            return res;
        }
    };
    ```

-   #### [LC78.子集](https://leetcode-cn.com/problems/subsets/)

    ```c++
    class Solution {
    public:
        vector<vector<int>> res;
        vector<int> path;
        void backtracing(vector<int>& nums, int startIndex) {
            // 求子集的问题需要把所有的节点的结果记录下来，不像组合的题目需要在叶子节点才记录结果
            res.push_back(path); // 需要放在终止条件上面
            if (startIndex >= nums.size()) return ;
            for (int i = startIndex; i < nums.size(); i++) {
                path.push_back(nums[i]);
                backtracing(nums, i + 1);
                path.pop_back();
            }
            return ;
        }
        vector<vector<int>> subsets(vector<int>& nums) {
            backtracing(nums, 0);
            return res;
        }
    };
    ```

-   [LC90.子集 II ](https://leetcode-cn.com/problems/subsets-ii/)

    ```c++
    class Solution {
    public:
        /*
         * 由题意知，需要在上一题的基础上去重，使不同的子集不能重复，子集内的元素可以重复，类似组合去重的思想一样
         */
        vector<vector<int>> res;
        vector<int> path;
        bool used[15] ={0};
        void backtracing(vector<int>& nums, int startIndex) {
            res.push_back(path); // 子集问题需要记录每一个节点的结果
            if (startIndex >= nums.size()) return ;
            for (int i = startIndex; i < nums.size(); i++) {
                if (i != 0 && nums[i] == nums[i - 1] && used[i - 1] == false) continue; // 避免树层上出现重复元素，去重 
                used[i] = true; // 保证树枝上可以出现重复元素
                path.push_back(nums[i]);
                backtracing(nums, i + 1);
                path.pop_back(); 
                used[i] = false;
            }
            return ;
        }
        vector<vector<int>> subsetsWithDup(vector<int>& nums) {
            sort(nums.begin(), nums.end());
            backtracing(nums, 0);
            return res;
        }
    };
    ```

-   #### [LC491.递增子序列](https://leetcode-cn.com/problems/increasing-subsequences/)

    ```c++
    class Solution {
    public:
        /*
         * 本题类似于上一题，但是不能用上一题的方法，此题不能对原数组进行排序
         * 需要使用别的方式给树层去重
         */
        vector<vector<int>> res;
        vector<int> path;
        void backtracing(vector<int>& nums, int startIndex) {
            if (path.size() >= 2) res.push_back(path); // 和求子集的一样
            if (startIndex >= nums.size()) return ; // 终止条件
            unordered_set<int> uset; // 按树枝的方向，梅递归一次就要重新初始化uset，这样在同一树枝可以保证可以重复使用元素
            for (int i = startIndex; i < nums.size(); i++) {
                if ((!path.empty() && nums[i] < path.back()) || uset.find(nums[i]) != uset.end()) continue; // 如果当前不是增序或者同一层中前面有使用过，直接跳过，对树层进行去重
                uset.insert(nums[i]); // 标记此树层已经使用，同一树层不会被重新初始化，只有换层才会初始化，并且此处后面不用erase，因为每次递归都会重新初始化
                path.push_back(nums[i]);
                backtracing(nums, i + 1);
                path.pop_back();
            }
            return ;
        }
        vector<vector<int>> findSubsequences(vector<int>& nums) {
            backtracing(nums, 0);
            return res;
        }
    };
    ```

-   #### [LC46.全排列](https://leetcode-cn.com/problems/permutations/)

    ```c++
    class Solution {
    public:
        vector<vector<int>> res;
        vector<int> path;
        int used[10] = {0}; // 去重数组
        void backtracing(vector<int>& nums) {
            if (path.size() == nums.size()) { // 终止条件：全排类
                res.push_back(path);
                return ;
            }
            for (int i = 0; i < nums.size(); i++) { // 每层都是从0开始
                if (used[i] == true) continue; // 如果此层使用过了，后面的就不能使用了
                used[i]  = true;
                path.push_back(nums[i]);
                backtracing(nums);
                path.pop_back();
                used[i] = false;
            }
            return ;
        }
        vector<vector<int>> permute(vector<int>& nums) {
            backtracing(nums);
            return res;
        }
    };
    ```

-   #### [LC47.全排列 II](https://leetcode-cn.com/problems/permutations-ii/)

    ```c++
    class Solution {
    public:
        // 在上一题的基础上，在树层上还需要去重,树层去重需要排序
        vector<vector<int>> res;
        vector<int> path;
        bool used[10] = {0};
        bool uset[10] = {0};
        void backtracing(vector<int>& nums) {
            if (path.size() == nums.size()) { // 终止条件
                res.push_back(path);
                return ;
            }
            for (int i = 0; i < nums.size(); i++) { 
                if (i != 0 && nums[i] == nums[i - 1] && used[i - 1] == false || used[i] == true) continue; // 树层去重避免选择相同值的不同位置的元素，只有树层方向的遍历有关，不影响树枝的操作，上一个树枝递归结束后used[i - 1] = false，又因为i是按照顺序进行的，说明前面nums[i-1]在本层已经使用过了
                // used[i] == true 树枝避免重复选择某一个位置的元素，只与树枝方向的递归有关
                used[i] = true;
                path.push_back(nums[i]);
                backtracing(nums);
                path.pop_back();
                used[i] = false;
            }
        }
        vector<vector<int>> permuteUnique(vector<int>& nums) {
            sort(nums.begin(), nums.end());
            backtracing(nums);
            return res;
        }
    };
    ```
    
-   #### [LC332.重新安排行程](https://leetcode-cn.com/problems/reconstruct-itinerary/)

    ![332.重新安排行程1](https://dongqing1903.oss-cn-shanghai.aliyuncs.com/img/2020111518065555.png)

    ```c++
    class Solution {
    public:
        unordered_map<string, map<string, int>> targets; // map可以满足排序问题
        vector<string> res;
        bool backtracking(int ticketsNum) {
            if (ticketsNum + 1 == res.size()) {
                return true;
            }
            for (auto &ticket : targets[res[res.size() - 1]]) {
                if (ticket.second > 0) {
                    res.push_back(ticket.first);
                    ticket.second -= 1;
                    if (backtracking(ticketsNum)) return true;
                    ticket.second += 1;
                    res.pop_back();
                }
            }
            return false;
        }
        vector<string> findItinerary(vector<vector<string>>& tickets) {
            for (auto i : tickets) targets[i[0]][i[1]] += 1;
            res.push_back("JFK");  // 加入起点
            backtracking(tickets.size());
            return res;
        }
    };
    ```

-   #### [LC51.N 皇后](https://leetcode-cn.com/problems/n-queens/)

    ![51.N皇后](https://dongqing1903.oss-cn-shanghai.aliyuncs.com/img/20210130182532303.jpg)

    ```c++
    class Solution {
    public:
        vector<vector<string>> res;
        /*
         * 纵向用递归，横向用遍历
         */
        void backtracking(int n, int row, vector<string>& path) {
            if (row == n) { // 终止条件：
                res.push_back(path);
                return ;
            }
            for (int col = 0; col < n; col++) { // 每一层都从头遍历，验证是否合适
                if (isValid(path, row, col, n)) {
                    path[row][col] = 'Q';
                    backtracking(n, row + 1, path);
                    path[row][col] = '.';
                }
            }
            return ;
        }
        bool isValid(vector<string>& path, int row, int col, int n) {
            // 不需要行检查，因为每一行只会选择一个元素，上一个元素选择完后会回复之前的棋盘
            for (int i = 0; i < row; i++) { // 列检查
                if (path[i][col] == 'Q') return false;
            }
            for (int i = row - 1, j = col - 1; i >= 0 && j >= 0; i--, j--) { // 135°方向
                if (path[i][j] == 'Q') return false;
            }
            for (int i = row - 1, j = col + 1; i >= 0 && j < n; i--, j++) { // 45°方向
                if (path[i][j] == 'Q') return false;
            }
            return true;
        }
        vector<vector<string>> solveNQueens(int n) {
            vector<string> path(n, string(n, '.'));
            backtracking(n, 0, path);
            return res;
        }
    };
    ```

-   #### [LC37.解数独](https://leetcode-cn.com/problems/sudoku-solver/)

    ```c++
    class Solution {
    public:
        bool backtracking(vector<vector<char>>& board) {
            for (int i = 0; i < board.size(); i++) {
                for (int j = 0; j < board[0].size(); j++) {
                    if (board[i][j] != '.') continue;
                    for (char k = '1'; k <= '9'; k++) {
                        if (isValid(board, i, j, k)) {
                            board[i][j] = k;
                            if (backtracking(board)) return true;
                            board[i][j] = '.';
                        }
                    }
                    return false;
                }
            }
            return true; //  
        }
        bool isValid(vector<vector<char>>& board, int row, int col, char k) {
            // 行检查
            for (int i = 0; i < 9; i++) {
                if (board[row][i] == k) return false;
            } 
            for (int i = 0; i < 9; i++) {
                if (board[i][col] == k) return false;
            }
            for (int i = (row / 3 * 3), ii = 0; ii < 3; i++, ii++) {
                for (int j = (col / 3 * 3), jj = 0; jj < 3; j++, jj++) {
                    if (board[i][j] == k) return false;
                }
            }
            return true;
        }
        void solveSudoku(vector<vector<char>>& board) {
            backtracking(board);
        }
    };
    ```


## 贪心算法

### 1、重点题型

-   #### [LC455.分发饼干](https://leetcode-cn.com/problems/assign-cookies/)

    ```c++
    class Solution {
    public:
        // 思想：大饼干优先给大胃口的孩子
        int findContentChildren(vector<int>& g, vector<int>& s) {
            sort(g.begin(), g.end()); 
            sort(s.begin(), s.end());
            int count = 0;
            int j = s.size() - 1;
            // 以胃口为基准，找到符合饼干的胃口
            for (int i = g.size() - 1; i >= 0; i--) {
                if (j >= 0 && g[i] <= s[j]) {
                    count += 1;
                    j--;
                }
            }
            return count;
        }
    };
    ```

    ```c++
    class Solution {
    public:
        int findContentChildren(vector<int>& g, vector<int>& s) {
            sort(g.begin(), g.end());
            sort(s.begin(), s.end());
            int count = 0;
            int j = g.size() - 1;
            // 以饼干为基础，找到符合饼干的最大胃口
            for (int i = s.size() - 1; i >= 0; i--) {
                while (j >= 0 && s[i] < g[j]) j--; // 找到最大的饼干能满足的最大胃口
                if (j < 0) break;
                count += 1;
                j--;
            }
            return count;
        }
    };
    ```

-   #### [LC376.摆动序列](https://leetcode-cn.com/problems/wiggle-subsequence/)

    ![376.摆动序列](https://dongqing1903.oss-cn-shanghai.aliyuncs.com/img/20201124174327597.png)

    ```c++
    class Solution {
    public:
        /*
         * 思想：删除单调坡上的节点 ==>> 只需记录有峰值变化的地方，出现峰值的地方
         */
        int wiggleMaxLength(vector<int>& nums) {
            int preDif = 0;
            int curDif = 0;
            int count = 1;
            for (int i = 0; i < nums.size() - 1; i++) {
                curDif = nums[i + 1] - nums[i];
                if (preDif <= 0 && curDif > 0 || (preDif >= 0 && curDif < 0)) {
                    count += 1;
                    preDif = curDif;
                }
            }
            return count;
        }
    };
    ```

-   #### [LC53.最大子数组和](https://leetcode-cn.com/problems/maximum-subarray/)

    ```c++
    class Solution {
    public:
        /*
         * 当前的sum < 0时，从头开始计数，要不然只会拉低总和	
         */
        int maxSubArray(vector<int>& nums) {
            int sum = 0;
            int maxSum = INT_MIN;
            for (int i = 0; i < nums.size(); i++) {
                sum += nums[i];
                maxSum = max(maxSum, sum);
                if (sum < 0) sum = 0;  
            }
            return maxSum;
        }
    };
    ```

-   #### [LC122.买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

    ```c++
    class Solution {
    public:
        /*
         * 思想：只需记录差值为正值得情况
         * 利益是可以拆解的，prices[3] - prices[0] = prices[3] - prices[2] + prices[2] - prices[1] + prices[1] - prices[0]
         */
        int maxProfit(vector<int>& prices) {
            int sum = 0;
            for (int i = 0; i < prices.size() - 1; i++) {
                if (prices[i + 1] - prices[i] > 0) {
                    sum += prices[i + 1] - prices[i];
                }
            }
            return sum;
        }
    };
    ```

-   #### [LC55.跳跃游戏](https://leetcode-cn.com/problems/jump-game/)

    ```c++
    class Solution {
    public:
        bool canJump(vector<int>& nums) {
            int maxIndex = 0;
            // 处理特殊情况
            if (nums.size() == 1) return true;
            // 只需求能够跳跃的最远距离是否会覆盖掉终点，不用追求每次跳几步
            for (int i = 0; i <= maxIndex; i++) {
                if (maxIndex >= nums.size() - 1) return true;
                maxIndex = max(i + nums[i], maxIndex);  
            }
            return false;
        }
    };
    ```

-   #### [LC45.跳跃游戏 II](https://leetcode-cn.com/problems/jump-game-ii/)

    ```c++
    class Solution {
    public:
        int jump(vector<int>& nums) {
            int curDis = 0;
            int nextDis = 0;
            int count = 0;
            /*
             * 从前往后遍历，只要是当前位置的最远距离能覆盖终点，就结束查找
             */
            for (int i = 0; i < nums.size() - 1; i++) {
                nextDis = max(nextDis, nums[i] + i);
                if (i == curDis) { // 遍历到当前最远覆盖位置
                    if (i < nums.size() - 1) { // 如果没有覆盖终点的话，就需要增加一步
                        count += 1;
                        curDis = nextDis;
                    } else break;
                }
            }
            return count;
        }
    };
    ```

    ```c++
    class Solution {
    public:
        int jump(vector<int>& nums) {
            int curDis = 0, nextDis = 0;
            int count = 0;
            /*
             * 把所有的元素都遍历一遍，肯定能覆盖终点，一步确定能最远的距离，每次到达最远覆盖位置就要增加一步
             */
            for(int i = 0; i < nums.size() - 1; i++) {
                nextDis = max(nums[i] + i, nextDis);
                if (i == curDis) {
                    curDis = nextDis;
                    count += 1;
                }
            }
            return count;
        }
    };
    ```

-   #### [LC1005.K 次取反后最大化的数组和](https://leetcode-cn.com/problems/maximize-sum-of-array-after-k-negations/)

    ```c++
    class Solution {
    public:
    static bool cmp(const int &a, const int &b) {
            return abs(a) < abs(b);
        }
        int largestSumAfterKNegations(vector<int>& nums, int k) {
            sort(nums.begin(), nums.end(), cmp);
            int sum = 0;
            for (int i = nums.size() - 1; i >= 0; i--) {
                if (nums[i] < 0 && k > 0) {
                    nums[i] *= -1;
                    k -= 1;
                } 
            }
            if (k % 2 == 1) nums[0] *= -1;
            for (auto i : nums) sum += i;
            return sum;
        }
    };
    ```
    
-   #### [LC134.加油站](https://leetcode-cn.com/problems/gas-station/)

    ```c++
    class Solution {
    public:
        int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
            /*
             * 情况1：总油都不够用
             * 情况2：最小剩余油量都是大于0的，起始位置就是0
             * 情况3：最小剩余油量小于0，从后往前找能够填补掉min的位置，因为总量是够用的，前面缺油后面肯定剩油，并且剩的油肯定能填补前面缺少的油
             */
            int curSum = 0;
            int minRest = INT_MAX;
            for (int i = 0; i < gas.size(); i++) {
                int rest = gas[i] - cost[i];
                curSum += rest;
                minRest = min(curSum, minRest);
            }
            if (curSum < 0) return -1;
            if (minRest > 0) return 0;
            for (int i = gas.size() - 1; i >= 0; i--) {
                minRest += gas[i] - cost[i];
                if (minRest >= 0) return i;
            }
            return -1;
        }
    };
    ```

    ```c++
    class Solution {
    public:
        int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
            /*
             * 从局部最优出发，如果当前油的总剩余量<0，说明当前位置和之前的所有位置都是不能用的，只能从下一个位置重新开始
             */
            int curSum = 0, totalSum = 0, index = 0;
            for (int i = 0; i < gas.size(); i++) {
                int rest = gas[i] - cost[i];
                totalSum += rest;
                curSum += rest;
                if (curSum < 0) {
                    curSum = 0;
                    index = i + 1;
                }
            }
            if (totalSum < 0) return -1;
            return index;
        }
    };
    ```

-   #### [LC135.分发糖果](https://leetcode-cn.com/problems/candy/)

    ```c++
    class Solution {
    public:
        int candy(vector<int>& ratings) {
            /*
             * 一次是从左到右遍历，只比较右边孩子评分比左边大的情况。
             * 一次是从右到左遍历，只比较左边孩子评分比右边大的情况。
             */
            vector<int> candies(ratings.size(), 1);
            for (int i = 1; i < ratings.size(); i++) {
                if (ratings[i] > ratings[i - 1]) candies[i] = candies[i - 1] + 1; // 从左往右遍历，只能改变右边的糖果，否则后面改变了，前面也改变不了
            }
            for (int i = ratings.size() - 2; i >= 0; i--) {
                if (ratings[i] > ratings[i + 1]) candies[i] = max(candies[i + 1] + 1, candies[i]); // 从右往左遍历，只能改变左边的糖果
            }
            int sum = 0;
            for (auto &i : candies) sum += i;
            return sum;
        }
    };
    ```

-   #### [LC860.柠檬水找零](https://leetcode-cn.com/problems/lemonade-change/)

    ```c++
    class Solution {
    public:
        bool lemonadeChange(vector<int>& bills) {
            /*
             * 情况一：账单是5，直接收下
    		 * 情况二：账单是10，消耗一个5，增加一个10
    		 * 情况三：账单是20，优先消耗一个10和一个5，如果不够，再消耗三个5
             */
            int cntFive = 0, cntTen = 0;
            for (int i = 0; i < bills.size(); i++) {
                if (bills[i] == 5) cntFive += 1; // 账单是5，直接收下
                else if (bills[i] == 10) { // 账单是10，消耗一个5，增加一个10
                    if (cntFive < 1) return false;
                    cntFive -= 1;
                    cntTen += 1;
                } else if (bills[i] == 20) { // 账单是20，优先消耗一个10和一个5，如果不够，再消耗三个5
                    if (cntTen >= 1 && cntFive >= 1) {
                        cntFive -= 1;
                        cntTen -= 1;
                    } else if (cntFive >= 3) {
                        cntFive -= 3;
                    } else return false;
                }
            }
            return true;
        }
    };
    ```

-   #### [LC406.根据身高重建队列](https://leetcode-cn.com/problems/queue-reconstruction-by-height/)

    ```c++
    class Solution {
    public:
        static bool cmp(const vector<int>& a, const vector<int>& b) {
            if (a[0] == b[0]) return a[1] < b[1];
            return a[0] > b[0];
        }
        vector<vector<int>> reconstructQueue(vector<vector<int>>& people) {
            /*
             * 两个维度：身高和属性
             * 思路：每次只处理一个维度
             * 步骤：先处理身高，按照身高由高向低排序，相同身高的按照属性由低向高排序，因为身高高的受其他的影响小，先固定升高高的，属性越少证明前面越靠前
             */
            sort(people.begin(), people.end(), cmp);
            vector<vector<int>> que;
            for (int i = 0; i < people.size(); i++) { // 从前往后遍历，依次按照属性插入
                que.insert(que.begin() + people[i][1], people[i]);
            }
            return que;
        }
    };
    ```

-   #### [LC452.用最少数量的箭引爆气球](https://leetcode-cn.com/problems/minimum-number-of-arrows-to-burst-balloons/)

    ```c++
    class Solution {
    public:
        static bool cmp(const vector<int>& a, const vector<int>& b) {
            if (a[0] == b[0]) return a[1] < b[1];
            return a[0] < b[0];
        }
        int findMinArrowShots(vector<vector<int>>& points) {
            sort(points.begin(), points.end());
            int count = 1;
            int curMax = points[0][1]; // curMax是同一支箭射下来所经过的气球的最短直径，也就代表箭的最右边的位置，如果有气球的直径起始位置在箭能发射的最右边的位置还要靠右，就需要重新一支箭了
            for (int i = 1; i < points.size(); i++) {
                if (curMax < points[i][0]) { // 
                    curMax = points[i][1];
                    count += 1; 
                } else if (curMax > points[i][1]) curMax = points[i][1]; // curMax不断更新同
            }
            return count;
        }
    };
    ```

-   #### [LC435.无重叠区间](https://leetcode-cn.com/problems/non-overlapping-intervals/)

    ```c++
    class Solution {
    public:
        static bool cmp(vector<int>& a, vector<int>& b) {
            if (a[1] == b[1]) return a[0] < b[0];
            return a[1] < b[1];
        }
        int eraseOverlapIntervals(vector<vector<int>>& intervals) {
            /*
             * 排序：按照右边界排序，右边界越小，后面的可选区间就越大，才能保证移除的区间数量最少
             * 遍历方向：从左往右
             * 通过求最大非重复区间个数来求
           	 * 需要分割点来做标记
             */
            sort(intervals.begin(), intervals.end(), cmp);
            int count = 1;
            int end = intervals[0][1]; // end作为分割点
            for (int i = 1; i < intervals.size(); i++) {
                if (end <= intervals[i][0]) { 
                    count += 1;
                    end = intervals[i][1]; // 此处越远更新end，代表end越小，对于后面的区间越有利
                }
            }
            return intervals.size() - count;
        }
    };
    ```

-   #### [LC763.划分字母区间](https://leetcode-cn.com/problems/partition-labels/)

    ```c++
    class Solution {
    public:
        vector<int> partitionLabels(string s) {
            /*
             * 思路：提前处理字符串，记录每个字母最后出现的位置
             * 标记：使用right用来标记当前遍历过的所有元素的最远位置，当遍历到最远位置right时就可以作为一个分段
             */
            int hash[30] = {0};
            vector<int> res;
            for (int i = 0; i < s.size(); i++) hash[s[i] - 'a'] = i; //记录每个元素出现的最远位置
            int left = 0, right = 0;
            for (int i = 0; i < s.size(); i++) { 
                right = max(right, hash[s[i] - 'a']); //记录最远位置
                if (i == right) { // 遍历到最远位置就就行分段
                    res.push_back(i - left + 1);
                    left = i + 1;
                }
            }
            return res;
        }
    };
    ```

-   #### [LC56.合并区间](https://leetcode-cn.com/problems/merge-intervals/)

    ```c++
    class Solution {
    public:
        static bool cmp(const vector<int>& a, const vector<int>& b) {
            if (a[0] == b[0]) return a[1] < b[1];
            return a[0] < b[0];
        }
        vector<vector<int>> merge(vector<vector<int>>& intervals) {
            /*
             * 思路：按照左边界排序，这样就可以确定起点，然后比对下一个区间的开始和上一个区间的结束
             */
            sort(intervals.begin(), intervals.end(), cmp);
            vector<vector<int>> res;
            bool flag = false; // 记录最后一个区间是否合并
            for (int i = 1; i < intervals.size(); i++) {
                int start = intervals[i - 1][0];
                int end = intervals[i - 1][1]; // 根据下一个区间更新终点
                while (i < intervals.size() && intervals[i][0] <= end) {
                    end = max(end, intervals[i][1]);
                    if (i == intervals.size() - 1) flag = true; // 说明最后一个区间被合并了
                    i += 1;
                }
                res.push_back(vector<int>{start, end});
            }
            if (flag == false) {
                res.push_back(vector<int>{intervals.back()[0], intervals.back()[1]});
            }
            return res;
        }
    };
    ```

-   #### [LC738.单调递增的数字](https://leetcode-cn.com/problems/monotone-increasing-digits/)

    ```c++
    class Solution {
    public:
        int monotoneIncreasingDigits(int n) {
            /*
             * 思路：从后往前遍历，如果strNum[i - 1] > strNUm[i]，strNum[i - 1] -= 1，同时strNum[i]及其之后所有的位数都可以变为9，用flag记录最左边的9位置
             */
            string strNum = to_string(n);
            int flag = strNum.size();
            for (int i = strNum.size() - 1; i >= 1; i--) {
                if (strNum[i - 1] > strNum[i]) {
                    strNum[i - 1] -= 1;
                    flag = i; // 记录最左边9的位置
                }
            }
            for (int i = flag; i < strNum.size(); i++) strNum[i] = '9'; // 把flag之后的所有位置换为9
            return stoi(strNum);
        }
    };
    ```

-   #### [LC714.买卖股票的最佳时机含手续费](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)

    ```c++
    class Solution {
    public:
        int maxProfit(vector<int>& prices, int fee) {
            /*
             * 情况一：收获利润的这一天并不是收获利润区间里的最后一天（不是真正的卖出，相当于持有股票），所以后面要继续收获利润
             * 情况二：前一天是收获利润区间里的最后一天（相当于真正的卖出了），今天要重新记录最小价格
             * 情况三：不作操作，保持原有状态（买入，卖出，不买不卖）
             */
            int res = 0;
            int minPrice = prices[0];
            for (int i = 1; i < prices.size(); i++) {
                if (minPrice > prices[i]) minPrice = prices[i]; // 情况二：
                if (prices[i] >= minPrice && prices[i] <= minPrice + fee) continue; // 情况三：只有有利润了，才卖出
                if (prices[i] > minPrice + fee) { // 情况一
                    res += prices[i] - minPrice - fee;
                    /*
                     * 1、如果minPrice不改变，就是连续收益，此时fee只要第一次时扣除，直到真正卖出为止
                     * 2、如果minPrice改变，情况二中，因为是重新买股票，需要手续费，由minPrice > Prices[i] ==>> prices[i] - minPrice - fee < prices[i] - minPrice' - fee ==>> 此时利润会更大
                     */
                    minPrice = prices[i] - fee; // 下一次算利润时就可以不算手续费
                }
            }
            return res;
        }
    };
    ```

-   #### [LC968.监控二叉树](https://leetcode-cn.com/problems/binary-tree-cameras/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        /*
         * 思路：摄像头不能放在叶子节点，需要放在树的中间层，才能保证摄像头数量最少
         * 遍历方式：后续遍历
         * 转移方式：一个接节点只有3种状态：0代表无覆盖，1代表有摄像头，2代表有覆盖
         */
        int result = 0;
        int traversal(TreeNode* root) {
            if (root == nullptr) return 2; // 空节点假定为有覆盖2，同时它的兄弟节点也是2，这样它的父节点就不用放摄像头，否则像叶子节点要放摄像头
            int left = traversal(root->left); // 后序遍历：左
            int right = traversal(root->right); // 后序遍历：右
    		// 后序遍历：中
            if (left == 2 && right == 2) return 0; // 情况一：左右孩子都有覆盖，该节点不需要放摄像头，可以通过该节点的父节点放摄像头来覆盖本节点
            if (left == 0 || right == 0) { // 情况二：左右节点有一个无覆盖，需要在该节点放摄像头
                result += 1;
                return 1;
            }
            if (left == 1 || right == 1) return 2; // 情况三：左右节点有摄像头，该节点就是有覆盖
            return -1;
        }
        int minCameraCover(TreeNode* root) {
            if (traversal(root) == 0) result += 1; // 情况四：特殊处理头节点
            return result;
        }
    };
    ```


## 动态规划

### 1、动态规划的理论要点

-   动态规划的解题步骤：
    1.  确定dp数组（dp table）以及下标的含义
    2.  确定递推公式
    3.  dp数组如何初始化
    4.  确定遍历顺序
    5.  举例推导dp数组
-   背包类型划分：![416.分割等和子集1](https://dongqing1903.oss-cn-shanghai.aliyuncs.com/img/20210117171307407.png)

### 2、重点题型

-   #### [LC509.斐波那契数](https://leetcode-cn.com/problems/fibonacci-number/)

    ```c++
    class Solution {
    public:
        int fib(int n) {
            /*
             * 1、dp[i]：当前值的斐波那契数值
             * 2、dp[i] = dp[i - 1] + dp[i - 2]
             * 3、由递推公式得知需初始化dp[0] dp[1]
             * 4、有递推公式知从前往后遍历
             */
            if (n <= 1) return n;
            vector<int> dp(n + 1);
            dp[0] = 0;
            dp[1] = 1;
            for (int i = 2; i <= n; i++) {
                dp[i] = dp[i - 1] + dp[i - 2];
            }
            return dp[n];
        }
    };
    ```

-   #### [LC70.爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)

    ```c++
    class Solution {
    public:
        int climbStairs(int n) {
            /*
             * 1、dp[i]：到达当前台阶的方法数量
             * 2、dp[i] = dp[i - 1] + dp[i - 2] ：上一个台阶和上上个台阶的方法数量决定
             * 3、由递推公式得知需初始化dp[1] dp[2]，dp[0]无意义
             * 4、有递推公式知从前往后遍历
             */
            if (n <= 1) return n;
            vector<int> dp(n + 1);
            dp[1] = 1;
            dp[2] = 2;
            for (int i = 3; i <= n; i++) {
                dp[i] = dp[i - 1] + dp[i - 2];
            }
            return dp[n];
        }
    };
    ```

-   #### [LC746.使用最小花费爬楼梯](https://leetcode-cn.com/problems/min-cost-climbing-stairs/)

    ```c++
    class Solution {
    public:
        int minCostClimbingStairs(vector<int>& cost) {
            /*
             * 1、dp[i]：只要往上爬，就需要消耗体力，第一步就需要消耗体力，到达i台阶的最小代价
             * 2、dp[i] = dp[i] = min(dp[i - 1] + cost[i - 1], dp[i - 2] + cost[i - 2])：上一个台阶和上上个台阶的到达本台阶的最小代价
             * 3、由递推公式得知需初始化dp[0] dp[1]
             * 4、有递推公式知从前往后遍历
             */
            vector<int> dp(cost.size() + 1);
            dp[0] = 0;
            dp[1] = 0;
            for (int i = 2; i <= cost.size(); i++) {
                dp[i] = min(dp[i - 1] + cost[i - 1], dp[i - 2] + cost[i - 2]);
            }
            return  dp[cost.size()];
        }
    };
    ```

-   #### [LC62.不同路径](https://leetcode-cn.com/problems/unique-paths/)

    ```c++
    class Solution {
    public:
        int uniquePaths(int m, int n) {
            /*
             * 1、dp[i][j]：到达[i,j]位置的路径总量
             */
            vector<vector<int>> dp(m, vector<int>(n));
            for (int i = 0; i < m; i++) dp[i][0] = 1; // 初始化：只能向下移动，只有一种方法
            for (int i = 0; i < n; i++) dp[0][i] = 1; // 初始化：只能向右移动，只有一种方法
            for (int i = 1; i < m; i++) {
                for (int j = 1; j < n; j++) {
                    dp[i][j] = dp[i - 1][j] + dp[i][j - 1]; // 递推公式
                }
            }
            return dp[m - 1][n - 1];
        }
    };
    ```

-   #### [LC63.不同路径 II](https://leetcode-cn.com/problems/unique-paths-ii/)

    ```c++
    class Solution {
    public:
        int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
            /*
             * 1、dp[i][j]：到达[i,j]位置的路径总量
             */
            int m = obstacleGrid.size(), n = obstacleGrid[0].size();
            vector<vector<int>> dp(m, vector<int>(n, 0));
            for (int i = 0; i < m && obstacleGrid[i][0] != 1; i++) dp[i][0] = 1; // 初始化：只能向下移动，只有一种方法，如果遇到障碍，下面的都到达不了
            for (int i = 0; i < n && obstacleGrid[0][i] != 1; i++) dp[0][i] = 1; // 初始化：只能向右移动，只有一种方法，如果遇到障碍，右面的都到达不了
            for (int i = 1; i < m; i++) {
                for (int j = 1; j < n; j++) {
                    if (obstacleGrid[i][j] == 1) continue; // 遇到障碍，保持0，说明从此方向的路径总量为0
                    dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
                }
            } 
            return dp[m - 1][n - 1];
        }
    };
    ```

-   #### [LC343.整数拆分](https://leetcode-cn.com/problems/integer-break/)

    ```c++
    class Solution {
    public:
        int integerBreak(int n) {
            /*
             * 1、dp[i]：拆分i可以获得的最大乘积
             * 2、dp[i]主要来源是dp[i - j] * j或(i - j) * j，(i - j) * j说明把i分解为两个数，dp[i - j] * j说明把i分解为多个数，dp[i - j]在前面遍历中已经确定
             */
            vector<int> dp(n + 1);
            dp[2] = 1;
            for (int i = 3; i <= n; i++) {
                for (int j = 1; j <= i - 1; j++) {
                    dp[i] = max(dp[i], max(dp[i - j] * j, (i - j) * j)); // 在指定不同的j时，对应dp[i]也不同，需要max(dp[i],  )
                }
            }
            return dp[n];
        }
    };
    ```

-   #### [LC96.不同的二叉搜索树](https://leetcode-cn.com/problems/unique-binary-search-trees/)

    ![96.不同的二叉搜索树2](https://dongqing1903.oss-cn-shanghai.aliyuncs.com/img/20210107093226241.png)

    ```c++
    class Solution {
    public:
        int numTrees(int n) {
            /*
             * 1、dp[i]：1到i为节点组成二叉树搜索树的个数
             * 2、dp[i] += dp[j - 1] * dp[i - j]：以不同值为头节点的情况总和，看上图
             * 3、dp[0] = 1，由递推公式知，只需要初始化dp[0]
             */
            vector<int> dp(n + 1);
            dp[0] = 1;
            for (int i = 1; i <= n; i++) {
                for (int j = 1; j <= i; j++) {
                    dp[i] += dp[j - 1] * dp[i - j];
                }
            }
            return dp[n];
        }
    };
    ```

-   #### [LC416.分割等和子集](https://leetcode-cn.com/problems/partition-equal-subset-sum/)

    ```c++
    class Solution {
    public:
        bool canPartition(vector<int>& nums) {
            /* 
             * 由题意知，只需判断是否存在某几个数之和恰好为总和的一半，可以使用0-1背包，以和为背包容量，每个数代表物品，数的大小代表物品的价值
             */
            int sum = 0, target = 0;
            for (auto i : nums) sum += i;
            if (sum % 2 == 1) return false; // 特殊判断
            target = sum / 2;
            vector<int> dp(target + 1, 0);
            for (int i = 0; i < nums.size(); i++) {
                for (int j = target; j >= nums[i]; j--) {
                    dp[j] = max(dp[j], dp[j - nums[i]] + nums[i]);
                }
            }
            return dp[target] == target ? true : false;
        }
    };
    ```

-   #### [LC1049.最后一块石头的重量 II](https://leetcode-cn.com/problems/last-stone-weight-ii/)

    ```c++
    class Solution {
    public:
        int lastStoneWeightII(vector<int>& stones) {
            /*
             * 由题意知，只要划分为相等两组时，此时结果才会最小，可以使用0-1背包，和为背包容量，石头为物品，石头的大小为价值
             */
            int target = 0, sum = 0;
            for (auto i : stones) sum += i;
            target = sum / 2;
            vector<int> dp(target + 1, 0);
            for (int i = 0; i < stones.size(); i++) {
                for (int j = target; j >= stones[i]; j--) {
                    dp[j] = max(dp[j], dp[j - stones[i]] + stones[i]);
                }
            }
            return sum - dp[target] - dp[target]; // 由于target是下取整，sum - dp[target] >= dp[target]
        }
    };
    ```

-   #### [LC494.目标和](https://leetcode-cn.com/problems/target-sum/)

    ```c++
    class Solution {
    public:
        int findTargetSumWays(vector<int>& nums, int target) {
            /*
             * 思路：a = b + target ==>> a + a = a + b + target ==>> a = (a + b + target) / 2
             * 判断nums中有哪几种组合可以组合出(a + b + target) / 2的情况
             * dp[j]: 当前容量下，最多有几种方法
             * dp[j] += dp[j - nums[i]]: 背包解决排列组合问题的通用递推公式
             */
            int sum = 0, bagCol = 0;
            for (auto i : nums) sum += i;
            if ((sum + target) % 2 == 1) return 0;
            if (sum < abs(target)) return 0; // 特殊情况
            bagCol = (sum + target) / 2;
            vector<int> dp(bagCol + 1, 0);
            dp[0] = 1;
            for (int i = 0; i < nums.size(); i++) {
                for (int j = bagCol; j >= nums[i]; j--) {
                    dp[j] += dp[j - nums[i]];
                }
            }
            return dp[bagCol];
        }
    };
    ```

-   #### [LC474.一和零](https://leetcode-cn.com/problems/ones-and-zeroes/)

    ```c++
    class Solution {
    public:
        int findMaxForm(vector<string>& strs, int m, int n) {
            /*
             * 思路：
             */
            vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
            for (auto &str : strs) {
                int zeroNum = 0, oneNum = 0;
                for (auto ch : str) {
                    if (ch == '0') zeroNum += 1;
                    else oneNum += 1;
                }
                for (int i = m; i >= zeroNum; i--) {
                    for (int j = n; j >= oneNum; j--) {
                        dp[i][j] = max(dp[i][j], dp[i - zeroNum][j - oneNum] + 1);
                    }
                }
            }
            return dp[m][n];
        }
    };
    ```

-   #### [LC518.零钱兑换 II](https://leetcode-cn.com/problems/coin-change-2/)

    ```c++
    class Solution {
    public:
        int change(int amount, vector<int>& coins) {
            /*
             * 组合问题的完全背包需要先遍历物品，这样可以保证同一背包下不会出现重复情况，组合是没有顺序的
             * 排列问题的完全背包需要先遍历背包，这样可以保证排列出所有的情况，排列是有顺序的
             */
            vector<int> dp(amount + 1);
            dp[0] = 1;
            for (int i = 0; i < coins.size(); i++) {
                for (int j = coins[i]; j <= amount; j++) {
                    dp[j] += dp[j - coins[i]];
                }
            }
            return dp[amount];
        }
    };
    ```

-   #### [LC377. 组合总和 Ⅳ](https://leetcode-cn.com/problems/combination-sum-iv/)

    ```c++
    class Solution {
    public:
        int combinationSum4(vector<int>& nums, int target) {
            /*
             * 组合问题的完全背包需要先遍历物品，这样可以保证同一背包下不会出现重复情况，组合是没有顺序的
             * 排列问题的完全背包需要先遍历背包，这样可以保证排列出所有的情况，排列是有顺序的
             */
            vector<int> dp(target + 1, 0);
            dp[0] = 1;
            for (int i = 0; i <= target; i++) { // 此题是排列问题
                for (int j = 0; j < nums.size(); j++) {
                     // 此处需要注意：答案保证最终答案的组合数在32位范围内，但是如果在taraget之前的数字组合数是可能超过INT_MAX的，甚至更大，但因为最终答案不会超过，所以target肯定不会利用到这些超过INT_MAX的数据的，所以忽略掉那些会超过INT_MAX的就可以
                    if (i >= nums[j] && dp[i] < INT_MAX - dp[i - nums[j]]) dp[i] += dp[i - nums[j]];
                }
            }
            return dp[target];
        }
    };
    ```

-   #### [LC322.零钱兑换](https://leetcode-cn.com/problems/coin-change/)

    ```c++
    class Solution {
    public:
        int coinChange(vector<int>& coins, int amount) {
            vector<int> dp(amount + 1, INT_MAX);
            dp[0] = 0;
            for (int i = 0; i < coins.size(); i++) {
                for (int j = coins[i]; j <= amount; j++) {
                    if (dp[j - coins[i]] != INT_MAX) // 避免整形溢出 
                        dp[j] = min(dp[j], dp[j - coins[i]] + 1);
                }
            }
            return dp[amount] == INT_MAX ? -1 : dp[amount];
        }
    };
    ```

-   #### [LC279.完全平方数](https://leetcode-cn.com/problems/perfect-squares/)

    ```c++
    class Solution {
    public:
        int numSquares(int n) {
            vector<int> dp(n + 1, INT_MAX);
            dp[0] = 0;
            for (int i = 0; i * i <= n; i++) {
                for (int j = i * i; j <= n; j++) {
                    if (dp[j - i * i] != INT_MAX) dp[j] = min(dp[j], dp[j - i * i] + 1);
                }
            }
            return dp[n];
        }
    };
    ```

-   #### [LC139.单词拆分](https://leetcode-cn.com/problems/word-break/)

    ```c++
    class Solution {
    public:
        bool wordBreak(string s, vector<string>& wordDict) {
            /*
             * dp[i]: 字符串s从0到i位置是否是wordDict的子集
             * 背包: 字符串
             * 物品: 单词
             */
            unordered_set<string> wordSet(wordDict.begin(), wordDict.end());
            vector<bool> dp(s.size() + 1, false);
            dp[0] = true;
            // 需要先遍历背包
            for (int i = 1; i <= s.size(); i++) {
                for (int j = 0; j < i; j++) {
                    string word = s.substr(j, i - j);
                    if (wordSet.find(word) != wordSet.end() && dp[j]) // dp[j]
                        dp[i] = true;
                }
            }
            return dp[s.size()];
        }
    };
    ```

-   #### [LC198.打家劫舍](https://leetcode-cn.com/problems/house-robber/)

    ```c++
    class Solution {
    public:
        int rob(vector<int>& nums) {
            if (nums.size() == 1) return nums[0]; // dp[i - 2]，这里需要单独判断
            vector<int> dp(nums.size(), 0);
            dp[0] = nums[0], dp[1] = max(nums[0], nums[1]);
            for (int i = 2; i < nums.size(); i++) {
                dp[i] = max(dp[i - 2] + nums[i], dp[i - 1]);
            }
            return dp[nums.size() - 1];
        }
    };
    ```

-   #### [LC213. 打家劫舍 II](https://leetcode-cn.com/problems/house-robber-ii/)

    ```c++
    class Solution {
    public:
        int rob(vector<int>& nums) {
            /*
             * 拆分为不包含头或不包含尾两种情况的打家劫舍I的问题
             */
            if (nums.size() == 0) return 0;
            if (nums.size() == 1) return nums[0];
            int res1 = robRange(nums, 0, nums.size() - 1); // 不包含尾
            int res2 = robRange(nums, 1, nums.size()); // 不包含头
            return max(res1, res2);
        }
        int robRange(vector<int>& nums, int start, int end) {
            if (end == start + 1) return nums[start];
            vector<int> dp(end - start + 1); // 这里不能是end - start，对于[1, nums.size()]来说，dp[i]是从下标1开始的
            dp[start] = nums[start];
            dp[start + 1] = max(nums[start], nums[start + 1]);
            for (int i = start + 2; i < end; i++) {
                dp[i] = max(dp[i - 2] + nums[i], dp[i - 1]);
            }
            return dp[end - 1];
        }
    };
    ```

-   #### [LC337.打家劫舍 III ](https://leetcode-cn.com/problems/house-robber-iii/)

    ```c++
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    class Solution {
    public:
        /*
         * 此题为树形dp，此题需要后序遍历，dp[2]用来记录每个节点偷与不偷的情况，使用后序遍历从底层将结果逐层向上递归传递vector<int> 
         */
        int rob(TreeNode* root) {
            vector<int> res = robTree(root);
            return max(res[0], res[1]);
        }
        // 下标0代表不偷的情况，下标1代表偷的情况
        vector<int> robTree(TreeNode *root) {
            if (root == nullptr) return vector<int>{0, 0};
            vector<int> left = robTree(root->left); // 遍历左子树
            vector<int> right = robTree(root->right); // 遍历右子树
            int val1 = root->val + left[0] + right[0]; // 偷
            int val2 = max(left[0], left[1]) + max(right[0], right[1]); // 不偷
            return vector<int>{val2, val1}; 
        }
    };
    ```

-   #### [LC121.买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)

    ```c++
    class Solution {
    public:
        /*
         * 动态规划：
         * 现金从最开始为0，买入股票现金为-prices[i]，卖出股票现金为所得利润
         * dp[i][0]：持有股票，所得的现金
         *	 第i-1天就持有股票，那么就保持现状，所得现金就是昨天持有股票的所得现金 即：dp[i - 1][0]
         *   第i天买入股票，所得现金就是买入今天的股票后所得现金即：-prices[i]
         * dp[i][1]：不持有股票，所得的现金
         *   第i-1天就不持有股票，那么就保持现状，所得现金就是昨天不持有股票的所得现金 即：dp[i - 1][1]
         *   第i天卖出股票，所得现金就是按照今天股票佳价格卖出后所得现金即：prices[i] + dp[i - 1][0] 
         */
        int maxProfit(vector<int>& prices) {
            vector<vector<int>> dp(prices.size(), vector<int>(2));
            dp[0][0] -= prices[0];
            dp[0][1] = 0;
            for (int i = 1; i < prices.size(); i++) {
                dp[i][0] = max(dp[i - 1][0], -prices[i]); // 0代表持有股票
                dp[i][1] = max(dp[i - 1][1], prices[i] + dp[i - 1][0]); // 1代表不持有股票
            }
            return dp[prices.size() - 1][1]; // 最后肯定是不持有股票
        }
    };
    ```

    ```c++
    class Solution {
    public:
        /*
         * 贪心算法
         */
        int maxProfit(vector<int>& prices) {
            int low = INT_MAX, maxPro = 0;
            for (int i = 0; i < prices.size(); i++) {
                low = min(low, prices[i]); // 记录最左边的最小值
                maxPro = max(maxPro, prices[i] - low);
            }
            return maxPro;
        }
    };
    ```

-   #### [LC122.买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

    ```c++
    class Solution {
    public:
        int maxProfit(vector<int>& prices) {
            /*
             * 思路：动态规划，dp[i][0]持有股票，dp[i][1]不持有股票
             * 递推公式：
             * dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] - prices[i])
             *   第i-1天就持有股票，那么就保持现状，所得现金就是昨天持有股票的所得现金 即：dp[i - 1][0]
             *	 第i天买入股票，所得现金就是昨天不持有股票的所得现金减去 今天的股票价格 即：dp[i - 1][1] - prices[i]  
             * dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] + prices[i])
             *	 第i-1天就不持有股票，那么就保持现状，所得现金就是昨天不持有股票的所得现金 即：dp[i - 1][1]
             *	 第i天卖出股票，所得现金就是按照今天股票佳价格卖出后所得现金即：prices[i] + dp[i - 1][0]
             */
            vector<vector<int>> dp(prices.size(), vector<int>(2));
            dp[0][0] -= prices[0] ; // 持有股票
            dp[0][1] = 0; // 不持有股票
            for (int i = 1; i < prices.size(); i++) {
                dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] - prices[i]);
                dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] + prices[i]);
            }
            return dp[prices.size() - 1][1];
        }
    };
    ```

-   #### [LC123.买卖股票的最佳时机 III](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii/)

    ```c++
    class Solution {
    public:
        int maxProfit(vector<int>& prices) {
            /*
             * 1、确定dp数组以及下标的含义：一天一共就有五个状态， 0. 没有操作
    		 *			第一次买入
    		 *			第一次卖出
    		 *			第二次买入
    		 *			第二次卖出
    		 *		dp[i][j]中 i表示第i天，j为 [0 - 4] 五个状态，dp[i][j]表示第i天状态j所剩最大现金。
    		 * 2、递推公式：
    		 *	  dp[i][1] = max(dp[i-1][0] - prices[i], dp[i - 1][1])
    		 *		操作一：第i天买入股票了，那么dp[i][1] = dp[i-1][0] - prices[i]
    		 *		操作二：第i天没有操作，而是沿用前一天买入的状态，即：dp[i][1] = dp[i - 1][1]
    		 *	  dp[i][2] = max(dp[i - 1][1] + prices[i], dp[i - 1][2])
    		 *		操作一：第i天卖出股票了，那么dp[i][2] = dp[i - 1][1] + prices[i]
    		 *		操作二：第i天没有操作，沿用前一天卖出股票的状态，即：dp[i][2] = dp[i - 1][2]
             */
            vector<vector<int>> dp(prices.size(), vector<int>(5));
            dp[0][0] = 0;
            dp[0][1] = -prices[0];
            dp[0][2] = 0;
            dp[0][3] = -prices[0];
            dp[0][4] = 0;
            for (int i = 1; i < prices.size(); i++) {
                // dp[i][0] = dp[i - 1][0]; // 没有操作，就不考虑此处是否
                dp[i][1] = max(dp[i - 1][1], 0 - prices[i]); // 因为是第一次买入股票，所以前面的dp[i][0] = 0，当前的现金是0 - prices[i]
                dp[i][2] = max(dp[i - 1][2], dp[i - 1][1] + prices[i]);
                dp[i][3] = max(dp[i - 1][3], dp[i - 1][2] - prices[i]);
                dp[i][4] = max(dp[i - 1][4], dp[i - 1][3] + prices[i]);
            }
            return dp[prices.size() - 1][4];
        }
    };
    ```

-   #### [LC188.买卖股票的最佳时机 IV](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iv/)

    ```c++
    class Solution {
    public:
        int maxProfit(int k, vector<int>& prices) {
            /*
             * 延续买卖股票的最佳时机III的思路
             */
            if (prices.size() == 0) return 0;
            vector<vector<int>> dp(prices.size(), vector<int>(2 * k + 1));
            for (int i = 1; i < 2 * k; i += 2) dp[0][i] -= prices[0]; // 只有买入时需要考虑
            for (int i = 1; i < prices.size(); i++) { 
                for (int j = 0; j < 2 * k - 1; j += 2) { // j的位置
                    // 不需要考虑dp[i][0]的情况，dp[i][0]至对于dp[i][1]有影响，而dp[i][1]表示第一次买入，相对应的dp[i][0]肯定为0，啥操作也没有
                    dp[i][j + 1] = max(dp[i - 1][j + 1], dp[i - 1][j] - prices[i]); // 奇数就是买入的情况
                    dp[i][j + 2] = max(dp[i - 1][j + 2], dp[i - 1][j + 1] + prices[i]); // 偶数就是卖出的情况
                }
            } 
            return dp[prices.size() - 1][2 * k];
        }
    };
    ```

-   #### [LC309.最佳买卖股票时机含冷冻期](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

    ```c++
    class Solution {
    public:
        int maxProfit(vector<int>& prices) {
            /*
             * dp[i][j]: 第i天状态为j，所剩的最多现金为dp[i][j]
             *      状态一 0：买入股票状态（今天买入股票，或者是之前就买入了股票然后没有操作）
             *      卖出股票状态，这里就有两种卖出股票状态
             *         状态二 1：两天前就卖出了股票，度过了冷冻期，一直没操作，今天保持卖出股票状态
             *         状态三 2：今天卖出了股票
             *      状态四 3：今天为冷冻期状态，但冷冻期状态不可持续，只有一天！
             */
            vector<vector<int>> dp(prices.size(), vector<int>(4));
            dp[0][0] -= prices[0];
            dp[0][1] = 0;
            dp[0][2] = 0;
            dp[0][3] = 0;
            for (int i = 1; i < prices.size(); i++) {
                /*
                 * 递推公式：
                 * 1、达到买入的状态: dp[i][0] = max(dp[i - 1][0], max(dp[i - 1][3], dp[i - 1][1]) - prices[i])
                 *      操作一：前一天就是持有股票状态（状态一），dp[i][0] = dp[i - 1][0]
                 *      操作二：今天买入了，有两种情况
                 *          前一天是冷冻期（状态四），dp[i - 1][3] - prices[i]
                 *          前一天是"保持"卖出股票状态（状态二），dp[i - 1][1] - prices[i]
                 * 2、达到"保持"卖出的状态：dp[i][1] = max(dp[i - 1][1], dp[i - 1][4])
                 *      操作一：前一天就是状态二
                 *      操作二：前一天是冷冻期（状态四）
                 * 3、达到今天就卖出的状态：dp[i][2] = dp[i - 1][0] + prices[i]
                 *      操作一：昨天一定是买入股票状态（状态一），今天卖出
                 * 4、达到冷冻期状态: dp[i][3] = dp[i - 1][2]
                 *      操作一：昨天卖出了股票（状态三）
                 */
                dp[i][0] = max(dp[i - 1][0], max(dp[i - 1][1], dp[i - 1][3]) - prices[i]);
                dp[i][1] = max(dp[i - 1][1], dp[i - 1][3]);
                dp[i][2] = dp[i - 1][0] + prices[i];
                dp[i][3] = dp[i - 1][2];
            }
            return max(dp[prices.size() - 1][1], max(dp[prices.size() - 1][2], dp[prices.size() - 1][3]));
    
        }
    };
    ```

-   #### [LC714.买卖股票的最佳时机含手续费](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)

    ```c++
    class Solution {
    public:
        int maxProfit(vector<int>& prices, int fee) {
            /*
             * 思路：买卖股票的最佳时机 II的基础上，只需要在卖出时减去fee
             */
            vector<vector<int>> dp(prices.size(), vector<int>(2));
            dp[0][0] -= prices[0] ; // 持有股票
            dp[0][1] = 0; // 不持有股票
            for (int i = 1; i < prices.size(); i++) {
                dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] - prices[i]);
                dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] + prices[i] - fee);
            }
            return dp[prices.size() - 1][1];
        }
    };
    ```

-   #### [LC300.最长递增子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

    ```c++
    class Solution {
    public:
        int lengthOfLIS(vector<int>& nums) {
            /*
             * dp[i]：i之前包括i的最长上升子序列的长度
             * 递推公式：dp[i] = max(dp[i], dp[j] + 1)
             */
            if (nums.size() <= 1) return nums.size();
            vector<int> dp(nums.size(), 1);
            int result = 0;
            for (int i = 1; i < nums.size(); i++) {
                for (int j = 0; j < i; j++) { // 通过j去更新dp[i]
                    if (nums[i] > nums[j]) dp[i] = max(dp[i], dp[j] + 1);
                }
                result = max(dp[i], result); // 记录中间最大值
            }
            return result;
        }
    };
    ```

-   #### [LC674.最长连续递增序列](https://leetcode-cn.com/problems/longest-continuous-increasing-subsequence/)

    ```c++
    class Solution {
    public:
        int findLengthOfLCIS(vector<int>& nums) {
            /*
             * dp[i]：以下标i为结尾的数组的连续递增子序列长度
             * dp[i] = dp[i - 1] + 1 或者 dp[i] = 1
             */
            if (nums.size() <= 1) return nums.size();
            vector<int> dp(nums.size(), 1);
            int result = 0;
            for (int i = 1; i < nums.size(); i++) {
                if (nums[i] > nums[i - 1]) dp[i] = dp[i - 1] + 1;
                result = max(dp[i], result);
            }
            return result;
        }
    };
    ```

-   #### [LC718.最长重复子数组](https://leetcode-cn.com/problems/maximum-length-of-repeated-subarray/)

    ```c++
    class Solution {
    public:
        int findLength(vector<int>& nums1, vector<int>& nums2) {
            /*
             * dp[i][j]：以下标i-1为结尾的nums1，和以下标j-1为结尾的nums2，最长重复子数组的长度
             * 如果dp[i][j]表示以下标i为结尾的nums1，和以下标j为结尾的nums2，最长重复子数组的长度的话，对于最后一个元素是无法记录下来的         
             */
            int result = 0;
            vector<vector<int>> dp(nums1.size() + 1, vector<int>(nums2.size() + 1, 0));
            for (int i = 1; i <= nums1.size(); i++) { // <=
                for (int j = 1; j <= nums2.size(); j++) { // <= 这样才能统计到最后一个元素是否重复的情况
                    if (nums1[i -1] == nums2[j - 1]) dp[i][j] = dp[i - 1][j - 1] + 1;
                    result = max(result, dp[i][j]);
                }
            }
            return result;
        }
    };
    ```

-   #### [LC1143.最长公共子序列](https://leetcode-cn.com/problems/longest-common-subsequence/)

    ```c++
    class Solution {
    public:
        int longestCommonSubsequence(string text1, string text2) {
            /*
             * dp[i][j]：长度为[0, i - 1]的字符串text1与长度为[0, j - 1]的字符串text2的最长公共子序列为dp[i][j]
             */
            vector<vector<int>> dp(text1.size() + 1, vector<int>(text2.size() + 1, 0));
            for (int i = 1; i <= text1.size(); i++) {
                for (int j = 1; j <= text2.size(); j++) {
                    if (text1[i - 1] == text2[j - 1]) dp[i][j] = dp[i - 1][j - 1] + 1;
                    else dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
            return dp[text1.size()][text2.size()];
        }
    };
    ```

    >   注意区分子数组和子序列：
    >
    >   -   子数组：连续的元素
    >   -   子序列：只是元素顺序和原数组的顺序相同，但可以不连续

-   #### [LC1035.不相交的线](https://leetcode-cn.com/problems/uncrossed-lines/)

    ```c++
    class Solution {
    public:
        int maxUncrossedLines(vector<int>& text1, vector<int>& text2) {
            /*
             * 思路：求绘制的最大连线数，其实就是求两个字符串的最长公共子序列的长度
             * dp[i][j]：长度为[0, i - 1]的字符串text1与长度为[0, j - 1]的字符串text2的最长公共子序列为dp[i][j]
             */
            vector<vector<int>> dp(text1.size() + 1, vector<int>(text2.size() + 1, 0));
            for (int i = 1; i <= text1.size(); i++) {
                for (int j = 1; j <= text2.size(); j++) {
                    if (text1[i - 1] == text2[j - 1]) dp[i][j] = dp[i - 1][j - 1] + 1;
                    else dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
            return dp[text1.size()][text2.size()];
        }
    };
    ```

-   #### [LC53.最大子数组和](https://leetcode-cn.com/problems/maximum-subarray/)

    ```c++
    class Solution {
    public:
        int maxSubArray(vector<int>& nums) {
            // dp[i]：包括下标i之前的最大连续子序列和为dp[i]
            if (nums.size() == 0) return 0;
            vector<int> dp(nums.size(), 0);
            dp[0] = nums[0];
            int result = dp[0]; 
            for (int i = 1; i < nums.size(); i++) {
                dp[i] = max(nums[i], nums[i] + dp[i - 1]);
                result = max(result, dp[i]);
            }
            return result;
        }
    };
    ```

-   #### [LC392.判断子序列](https://leetcode-cn.com/problems/is-subsequence/)

    ```c++
    class Solution {
    public:
        bool isSubsequence(string s, string t) {
            /*
             * 思路：和最长重复子数组的思路是一样的，寻找连续公共的部分
             */
            vector<vector<int>> dp(s.size() + 1, vector<int>(t.size() + 1));
            for (int i = 1; i <= s.size(); i++) {
                for (int j = 1; j <= t.size(); j++) {
                    if (s[i - 1] == t[j - 1]) dp[i][j] = dp[i- 1][j - 1] + 1;
                    else dp[i][j] = dp[i][j - 1]; // 不等时，需要删除t串中的当前元素
                }
            }
            return dp[s.size()][t.size()] == s.size() ? true : false;
        }
    };
    ```

-   #### [LC115.不同的子序列](https://leetcode-cn.com/problems/distinct-subsequences/)

    ```c++
    class Solution {
    public:
        int numDistinct(string s, string t) {
            /*
             * dp[i][j]：以i-1为结尾的s子序列中出现以j-1为结尾的t的个数为dp[i][j] <<<<这是一个连续的问题，不管i这个位置有没有和j这个位置匹配，最少可以保持i-1位置上的个数>>>>
             */
            vector<vector<unsigned long long>> dp(s.size() + 1, vector<unsigned long long>(t.size() + 1));
            for (int i = 0; i <= s.size(); i++) dp[i][0] = 1; // 根据推导公式得出，需要初始化的值
            for (int j = 1; j <= t.size(); j++) dp[0][j] = 0;
            for (int i = 1; i <= s.size(); i++) {
                for (int j = 1; j <= t.size(); j++) {
                    /*
                     * s[i - 1] == t[j - 1]的情况：包括两种情况
                     * 		1、一部分是用s[i - 1]来匹配，那么个数为dp[i - 1][j - 1]
                     *		2、一部分是不用s[i - 1]来匹配，个数为dp[i - 1][j]。
                     *	这两种情况不会出现重叠的部分，比如s：bagg 和 t：bag
                     *		s[2] == t[2]: 此时dp[i][j] = dp[i - 1][j - 1]的情况，考虑的是a这个位置的个数情况
                     *		s[3] == t[2]: 此时dp[i][j] = dp[i - 1][j]的情况，在i-2结尾的情况，已经匹配到j-1这个位置的个数情况
                     */
                    if (s[i - 1] == t[j - 1]) { 
                        dp[i][j] = dp[i - 1][j - 1] + dp[i - 1][j];
                    } else {
                        dp[i][j] = dp[i - 1][j];
                    }
                }
            }
            return dp[s.size()][t.size()];
        }
    };
    ```

-   #### [LC583.两个字符串的删除操作](https://leetcode-cn.com/problems/delete-operation-for-two-strings/)

    ```c++
    class Solution {
    public:
        int minDistance(string word1, string word2) {
            /*
             * dp[i][j]：以i-1为结尾的字符串word1，和以j-1位结尾的字符串word2，想要达到相等，所需要删除元素的最少次数
             */
            vector<vector<int>> dp(word1.size() + 1, vector<int>(word2.size() + 1));
            for (int i = 0; i <= word1.size(); i++) dp[i][0] = i;
            for (int j = 0; j <= word2.size(); j++) dp[0][j] = j;
            for (int i = 1; i <= word1.size(); i++) {
                for (int j = 1; j <= word2.size(); j++) {
                    /*
                     * word1[i - 1] == word2[j - 1]的情况：如果相等就不需要删除任何元素
                     * word1[i - 1] != word2[j - 1]的情况：
                     *		1、需要删除word1[i - 1]时：dp[i][j] = dp[i - 1][j] + 1
                     *		2、需要删除word2[j - 1]时：dp[i][j] = dp[i][j - 1] + 1
                     *		3、需要删除word1[i - 1]和word2[j - 1]时：dp[i][j] = dp[i - 1][j - 1] + 2
                     *		取这三种情况的最小值
                     */
                    if (word1[i - 1] == word2[j - 1]) dp[i][j] = dp[i - 1][j - 1];
                    else dp[i][j] = min(dp[i - 1][j] + 1, min(dp[i][j - 1] + 1, dp[i - 1][j - 1] + 2));
                }
            }
            return dp[word1.size()][word2.size()];
        }
    };
    ```

-   #### [LC72.编辑距离](https://leetcode-cn.com/problems/edit-distance/)

    ```c++
    class Solution {
    public:
        int minDistance(string word1, string word2) {
            /*
             * dp[i][j]：以下标i-1为结尾的字符串word1，和以下标j-1为结尾的字符串word2，最近编辑距离为dp[i][j]
             */
            vector<vector<int>> dp(word1.size() + 1, vector<int>(word2.size() + 1));
            for (int i = 0; i <= word1.size(); i++) dp[i][0] = i; 
            for (int j = 0; j <= word2.size(); j++) dp[0][j] = j; // 此时需要在word1中执行插入操作
            for (int i = 1; i <= word1.size(); i++) {
                for (int j = 1; j <= word2.size(); j++) {
                    /*
                     * 1、如果word1[i - 1] == word2[j - 1]，则什么也不用做dp[i][j] = dp[i - 1][j - 1]
                     * 2、如果word1[i - 1] != word2[j - 1]，则有三种情况：
                     *		1、删除word1[i - 1]，则dp[i][j] = dp[i - 1][j] + 1
                     *		2、插入word1[i - 1]，就相当于删除word2[j - 1]，两者操作次数是相等的，则dp[i][j] = dp[i][j - 1] + 1
                     *		3、替换word1[i - 1]，则dp[i][j] = dp[i - 1][j - 1] + 1
                     *	  取三种情况中的最小值，就是最小操作次数
                     */
                    if (word1[i - 1] == word2[j - 1]) dp[i][j] = dp[i - 1][j - 1];
                    else dp[i][j] = min(dp[i - 1][j] + 1, min(dp[i][j - 1] + 1, dp[i - 1][j - 1] + 1));
                }
            }
            return dp[word1.size()][word2.size()];
        }
    };
    ```

-   #### [LC647.回文子串](https://leetcode-cn.com/problems/palindromic-substrings/)

    ```c++
    class Solution {
    public:
        int countSubstrings(string s) {
            // dp[i][j]：表示区间范围[i,j] （注意是左闭右闭）的子串是否是回文子串，如果是dp[i][j]为true，否则为false
            vector<vector<bool>> dp(s.size(), vector<bool>(s.size()));
            int result = 0;
            /*
             * 遍历顺序：会用到没有计算过的dp[i + 1][j - 1]，也就是根据不确定是不是回文的区间[i+1,j-1]，来判断了[i,j]是不是回文，所以一定要从下到上，从左到右遍历，这样保证dp[i + 1][j - 1]都是经过计算的
             */
            for (int i = s.size() - 1; i >= 0; i--) {
                for (int j = i; j < s.size(); j++) {
                    /*
                     * 1、当s[i]与s[j]不相等，那没啥好说的了，dp[i][j]一定是false
                     * 2、当s[i]与s[j]相等时，这就复杂一些了，有如下三种情况
                     * 		情况一：下标i 与 j相同，同一个字符例如a，当然是回文子串
                     *		情况二：下标i 与 j相差为1，例如aa，也是文子串
                     *		情况三：下标：i 与 j相差大于1的时候，例如cabac，此时s[i]与s[j]已经相同了，我们看i到j区间是不是回文子串就看aba是不是回文就可以了，那么aba的区间就是 i+1 与 j-1区间，这个区间是不是回文就看dp[i + 1][j - 1]是否为true
                     */
                    if (s[i] == s[j]) {
                        if (j - i <= 1) {
                            result += 1;
                            dp[i][j] = true;
                        } else if (dp[i + 1][j - 1]) {
                            dp[i][j] = true;
                            result += 1;
                        }
                    }
                }
            }
            return result;
        }
    };
    ```

-   #### [LC516.最长回文子序列](https://leetcode-cn.com/problems/longest-palindromic-subsequence/)

    ```c++
    class Solution {
    public:
        int longestPalindromeSubseq(string s) {
            /*
             * dp[i][j]：字符串s在[i, j]范围内最长的回文子序列的长度为dp[i][j]
             */
            vector<vector<int>> dp(s.size(), vector<int>(s.size()));
            // dp[i][j] = dp[i + 1][j - 1] + 2; 可以看出 递推公式是计算不到 i 和j相同时候的情况
            for (int i = 0; i < s.size(); i++) dp[i][i] = 1;
            // dp[i][j]是依赖于dp[i + 1][j - 1] 和 dp[i + 1][j]，遍历i的时候一定要从下到上遍历，这样才能保证，下一行的数据是经过计算的
            for (int i = s.size() - 1; i >= 0; i--) {
                for (int j = i + 1; j < s.size(); j++) {
                    /*
                     * 1、如果s[i]与s[j]相同，那么dp[i][j] = dp[i + 1][j - 1] + 2
                     * 2、如果s[i]与s[j]不相同，说明s[i]和s[j]的同时加入并不能增加[i,j]区间回文子串的长度，那么分别加入s[i]、s[j]看看哪一个可以组成最长的回文子序列，dp[i][j]一定是取最大的
                     *		加入s[j]的回文子序列长度为dp[i + 1][j]
                     *		加入s[i]的回文子序列长度为dp[i][j - 1]
                     */
                    if (s[i] == s[j]) dp[i][j] = dp[i + 1][j - 1] + 2;
                    else dp[i][j] = max(dp[i + 1][j], dp[i][j - 1]);
                }
            }
            return dp[0][s.size() - 1];
        }
    };
    ```

## 深度搜索与广度搜索

### 1、深搜与广搜的理论要点





### 2、重点题型

-   #### [HZOJ535.瓷砖](http://oj.haizeix.com/problem/535)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    int dir[4][2] = {0, 1, 1, 0, 0, -1, -1, 0}; // 对应上下左右四个方向
    int n, m, sx, sy, ans = 1;
    char mmap[55][55];
    
    void func(int x, int y) { // 深搜就是递归，沿着一条路径一直递归下去
        for (int i = 0; i < 4; i++) { // 遍历当前位置的每一个方向上的下一个位置
            int xx = x + dir[i][0];
            int yy = y + dir[i][1];
            if (mmap[xx][yy] == '.') { // 遇到黑色瓷砖
                mmap[xx][yy] = '0'; // 标记已经计算过该位置，防止重复记录
                ans += 1;
                func(xx, yy); // 递归下一个位置
            }
        }
    }
    int main() {
        cin >> n >> m;
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                cin >> mmap[i][j];
                if (mmap[i][j] == '@') { // 记录起点信息
                    sx = i;
                    sy = j;
                }
            }
        }
        func(sx, sy);
        cout << ans << endl;
        return 0;
    }
    ```

-   #### [HZOJ397.僵尸来袭](http://oj.haizeix.com/problem/397)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    int dir[4][2] = {0, 1, 0, -1, 1, 0, -1, 0};
    int mmap[105][105];
    int m, n, ans;
    
    void func(int x, int y) {
        for (int i = 0; i < 4; i++) {
            int xx = x + dir[i][0];
            int yy = y + dir[i][1];
            if (mmap[xx][yy] != 0) {
                mmap[xx][yy] = 0; // 将一波僵尸全部置为0
                func(xx, yy);
            }
        }
        return ;
    }
    
    int main() {
        cin >> m >> n;
        for (int i = 1; i <= m; i++)
            for (int j = 1; j <= n; j++)
                cin >> mmap[i][j];
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (mmap[i][j] != 0) { 
                    ans += 1; // 一波将是算一次
                    mmap[i][j] = 0; // 深搜整个一波僵尸
                    func(i, j);
                }
            }
        }
        cout << ans << endl;
        return 0;
    }
    ```

-   #### [HZOJ536.最大黑色区域](http://oj.haizeix.com/problem/536)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    int m, n;
    int dir[4][2] = {0, 1, 0, -1, 1, 0, -1, 0};
    char mmap[105][105];
    int temp, ans;
    
    void func(int x, int y) {
        for (int i = 0; i < 4; i++) {
            int xx = x + dir[i][0];
            int yy = y + dir[i][1];
            if (mmap[xx][yy] == '1') {
                temp += 1;
                mmap[xx][yy] = '0';
                func(xx, yy);
            }
        }
        return ;
    }
    int main() {
        cin >> m >> n;
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                cin >> mmap[i][j];
            }
        }
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (mmap[i][j] == '1') {
                    mmap[i][j] = '0';
                    temp = 1;
                    func(i, j);
                    ans = max(ans, temp);
                }
            }
        }
        cout << ans << endl;
        return 0;
    }
    ```

-   #### [LC200.岛屿数量](https://leetcode-cn.com/problems/number-of-islands/)

    ```c++
    class Solution {
    public:
        int ans = 0, n, m;
        int dir[4][2] = {0, 1, 0, -1, 1, 0, -1, 0};
        void func(int x, int y, vector<vector<char>> &mmap) {
            for (int i = 0; i < 4; i++) {
                int xx = x + dir[i][0];
                int yy = y + dir[i][1];
                if (xx < 0 || yy < 0 || xx == n || yy == m) continue; // 考虑边界问题
                if (mmap[xx][yy] == '1') {
                    mmap[xx][yy] = '0';
                    func(xx, yy, mmap);
                }
            }
        }
        int numIslands(vector<vector<char>>& grid) {
            n = grid.size();
            m = grid[0].size();
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < m; j++) {
                    if (grid[i][j] == '1') {
                        ans += 1;
                        grid[i][j] = '0';
                        func(i, j, grid);
                    }
                }
            }
            return ans;
        }
    };
    ```

-   #### [HZOJ406.水坑数量](http://oj.haizeix.com/problem/406)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    int m, n, ans;
    char mmap[2005][2005];
    int dir[8][2] = {0, 1, 0, -1, 1, 0, -1, 0, 1, -1, -1, 1, 1, 1, -1, -1};
    
    void func(int x, int y) {
        for (int i = 0; i < 8; i++) {
            int xx = x + dir[i][0];
            int yy = y + dir[i][1];
            if (mmap[xx][yy] == '#') {
                mmap[xx][yy] = '.';
                func(xx, yy);
            }
        }
        return ;
    }
    
    int main() {
        cin >> m >> n;
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                cin >> mmap[i][j];
            }
        }
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (mmap[i][j] == '#') {
                    mmap[i][j] = '.';
                    ans += 1;
                    func(i, j);
                }
            }
        }
        cout << ans << endl;
        return 0;
    }
    ```

-   #### [HZOJ396. 填涂颜色](http://oj.haizeix.com/problem/396)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    /*
     * 思路：将外层的0改为3，这样这个图只有被1包围的地方是0
     */
    
    int n;
    int dir[4][2] = {0, 1, 0, -1, 1, 0, -1, 0};
    int mmap[35][35];
    
    void func(int x, int y) {
        for (int i = 0; i < 4; i++) {
            int xx = x + dir[i][0];
            int yy = y + dir[i][1];
            if (xx < 0 || yy < 0 || xx > n + 1 || yy > n + 1) continue; // 只需要把范围之内的0全部改为3即可，否则会把定义的整个数 组多余的部分也都改为了0，浪费时间
            if (mmap[xx][yy] == 0) {
                mmap[xx][yy] = 3;
                func(xx, yy);
            }
        }
        return ;
    }
    
    int main() {
        cin >> n;
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) {
                cin >> mmap[i][j];
            }
        }
    	
        func(0, 0); // 先把外层的0全部改成3
    
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) {
                j != 1 && cout << ' ';
                if (mmap[i][j] == 0) cout << 2;
                else if (mmap[i][j] == 3) cout << 0;
                else cout << 1;
            }
            cout << endl;
        }
        return 0;
    }
    ```

-   #### [HZOJ405.01迷宫](http://oj.haizeix.com/problem/405)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    /* 
     * 思路：因为是多组查询，可以将每一个位置上的情况都统计出来，然后直接查询结果，这样一次性统计出查询结果，会利用彼此间的位置关系，比一次已查询节省很多时间
     * 	1、通过mmap数组存储迷宫地图
     *	2、通过ans数组记录每个位置的查询结果
     *	3、使用顺序结构queue存储相连的位置，最后好一起更新ans值
     */
    struct node {
        int x, y;
    };
    
    int m, n, k, cnt;
    int dir[4][2] = {0, 1, 0, -1, 1, 0, -1, 0};
    char mmap[3005][3005];
    int ans[3005][3005];
    queue<node> que;
    
    void save() { // 更新所有的连接在一起的ans值
        while (!que.empty()) {
            node temp = que.front();
            que.pop();
            ans[temp.x][temp.y] = cnt;
        }
        return ;
    }
    
    void func(int x, int y) { 
        for (int i = 0; i < 4; i++) {
            int xx = x + dir[i][0];
            int yy = y + dir[i][1];
    
            if (xx < 1 || yy < 1 || xx > m || yy > n || ans[xx][yy] != 0) continue; // 需要考虑边界问题，以及ans[xx][yy]如果已经更新过答案的话，就不需要访问了
            if (mmap[xx][yy] != mmap[x][y]) {
                cnt += 1; 
                ans[xx][yy] = 1; // 标记此位置访问过了
                que.push(node{xx, yy}); // 将所有连接的位置统统放入队列，最后一起更新ans值
                func(xx, yy);
            }
        }
        return ;
    }
    
    int main() {
        scanf("%d%d%d", &m, &n, &k);
        for (int i = 1; i <= m; i++) {
            scanf("%s", &mmap[i][1]);
        }
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (ans[i][j] == 0) { // 当前位置没访问过
                    cnt = 1;
                    ans[i][j] = 1;
                    que.push(node{i, j});
                    func(i, j);
                    save();
                }
            }
        }
        for (int i = 0; i < k; i++) {
            int a, b;
            scanf("%d%d", &a, &b);
            printf("%d\n", ans[a][b]);
        }
    
        return 0;
    }
    ```

-   #### [HZOJ541.相遇问题](http://oj.haizeix.com/problem/541)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    /*
     * 思路：
     *   1、使用邻接矩阵记录牧场之间的花费时间：arr[2][20][20]，由于是两个人走，需要分别记录两个人走路的时间花费，2的位置代表哪一 个人走的
     *   2、ans[2][1000005]：记录每一个人从1号仓库到达本仓库的总花费情况
     *   3、cnt[n]：用于记录每一个人总共走了几种方案
     */
    int n, m, arr[2][20][20], ans[2][1000005], cnt[2];
    
    void func(int person, int now, int cost) {
        if (now == n) { // 走到尽头了
            ans[person][cnt[person]] = cost;
            cnt[person] += 1;
            return ;
        }
        for (int i = now + 1; i <= n; i++) { // 从当前仓库一直递归下去
            if (arr[person][now][i] != 0) {
                func(person, i, cost + arr[person][now][i]);
            }
        }
    }
    
    int main() {
        cin >> n >> m;
        for (int i = 0; i < m; i++) {
            int a, b, c, d;
            cin >> a >> b >> c >> d;
            arr[0][a][b] = arr[0][b][a] = c; // 两个方向都需要记录
            arr[1][a][b] = arr[1][b][a] = d;
        }
        func(0, 1, 0);
        func(1, 1, 0);
        sort(ans[0], ans[0] + cnt[0]);
        sort(ans[1], ans[1] + cnt[1]);
        for (int i = 0; i < cnt[0]; i++) {
            for (int j = 0; j < cnt[1]; j++) {
                if (ans[0][i] == ans[1][j]) {
                    cout << ans[0][i] << endl;
                    return 0;
                }
            }
        }
        cout << "IMPOSSIBLE" << endl;
        return 0;
    }
    ```

-   #### [HZOJ542.奶酪](http://oj.haizeix.com/problem/542)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    #include <cmath>
    using namespace std;
    // xyz记录每个点的x,y,z坐标值，mark标记哪个点被访问过了，arr邻接矩阵记录点与点之间是否联通，
    // s记录所有的起点，cnt记录起点的个数，e记录所有的终点，是终点的位置标记为1
    long long t, n, h, r, xyz[1005][3], mark[1005], arr[1005][1005], s[1005], cnt, e[1005];
    
    int func(int now) {
        if (e[now] == 1) { // 遍历到终点，表示可以实现
            return 1;
        }
        for (int i = 1; i <= n; i++) { 
            if (arr[now][i] == 1 && mark[i] == 0) { // 如果联通，并且没被访问过
                mark[i] = 1;
                if (func(i) == 1) return 1;
            }
        }
        return 0;
    }
    
    int main() {
        cin >> t;
        for (int i = 0; i < t; i++) { 
            memset(mark, 0, sizeof(mark)); // 因为要进行t次查询，每次都需要清零重新来
            memset(arr, 0, sizeof(arr));
            cnt = 0;
            memset(e, 0, sizeof(e));
            cin >> n >> h >> r;
            for (int i = 1; i <= n; i++) {
                cin >> xyz[i][0] >> xyz[i][1] >> xyz[i][2];
                if (xyz[i][2] <= r) { // 记录起点
                    s[cnt] = i;
                    cnt += 1;
                }
                if (xyz[i][2] + r >= h) { // 记录终点
                    e[i] = 1;
                }
                for (int j = 1; j < i; j++) { // 判断点与点之间是否联通
                    int t1 = xyz[i][0] - xyz[j][0];
                    int t2 = xyz[i][1] - xyz[j][1];
                    int t3 = xyz[i][2] - xyz[j][2];
                    double t4 = sqrt(t1 * t1 + t2 * t2 + t3 * t3);
                    if (t4 <= 2 * r) {
                        arr[i][j] = arr[j][i] = 1;
                    }
                }
            }
            int flag = 0;
            for (int i = 0; i < cnt; i++) { // 遍历所有的起点
                if (mark[s[i]] == 0) {
                    mark[s[i]] = 1;
                    if (func(s[i]) == 1) {
                        flag = 1;
                        cout << "Yes" << endl;
                        break;
                    }
                }
            }
            if (flag == 0) cout << "No" << endl;
        }
        return 0;
    }
    ```

-   #### [HZOJ538.图的遍历](http://oj.haizeix.com/problem/538)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    int n, flag;
    int arr[25][25], ans[25], mark[25];
    
    void func(int x) {
        if (flag == 1) { // 用来标记是否是第一次
            cout << "-";
        }
        flag = 1;
        cout << x;
        for (int i = 0; i <= n; i++) { // 遍历所有点，找到与此点相连的点进行深搜遍历
            if (arr[x][i] == 1 && mark[i] == 0) {
                mark[i] = 1;
                func(i);
            }
        }
        return ;
    }
    
    int main() {
        cin >> n;
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) {
                cin >> arr[i][j];
            }
        }
        for (int i = 1; i <= n; i++) { // 以所有的点为起点进行遍历
            if (mark[i] == 0) {
                mark[i] = 1;
                func(i);
            }
        }
        cout << endl;
        return 0;
    }
    ```


>   **总结**：岛屿数量这题需要考虑边界问题，其他海贼的题目不用考虑，因为多定义分边界之外的地方，有避免无限递归的问题， 图的搜索问题笔记

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

-   #### [HZOJ304.骑士风度的牛](http://oj.haizeix.com/problem/304)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    struct node {
        int x, y, step;
    };
    
    int m, n, startX, startY, endX, endY;
    int dir[8][2] = {1, 2, 1, -2, 2, 1, 2, -1, -1, 2, -1, -2, -2, -1, -2, 1};
    char mmap[200][200];
    
    
    int main() {
        cin >> n >> m; // 注意题目顺序中给的顺序
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                cin >> mmap[i][j];
                if (mmap[i][j] == 'K') { // 记录起点
                    startX = i;
                    startY = j;
                }
            }
        }
        queue<node> que;
        que.push(node{startX, startY, 0});
        while (!que.empty()) {
            node temp = que.front();
            que.pop();
            for (int i = 0; i < 8; i++) {
                int xx = temp.x + dir[i][0];
                int yy = temp.y + dir[i][1];
                if (xx < 1 || yy < 1 || xx > m || yy > n || mmap[xx][yy] == '*') continue; // 边界判断
                if (mmap[xx][yy] == 'H') {
                    cout << temp.step + 1 << endl;
                    return 0;
                }
                if (mmap[xx][yy] == '.') {
                    que.push(node{xx, yy, temp.step + 1});
                    mmap[xx][yy] = '*'; // 去重
                }
            }
        }
        return 0;
    }
    ```

-   #### [HZOJ398.马的遍历](http://oj.haizeix.com/problem/398)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    struct node {
        int x, y, step;
    };
    int n, m, x, y;
    int dir[8][2] = {1, 2, 1, -2, -1, 2, -1, -2, 2, 1, 2, -1, -2, 1, -2, -1};
    int ans[450][450];
    queue<node> que;
    
    int main() {
        cin >> n >> m >> x >> y;
        memset(ans, -1, sizeof(ans)); // 初始化为-1，如果初始化为0，对于起点的去重无法判断
        ans[x][y] = 0; // 起点
        que.push(node{x, y, 0}); // 将起点压入队列中
        while (!que.empty()) {
            node temp = que.front();
            que.pop();
            for (int i = 0; i < 8; i++) { // 遍历8个方向
                int xx = temp.x + dir[i][0];
                int yy = temp.y + dir[i][1];
                if (xx < 1 || yy < 1 || xx > n || yy > m || ans[xx][yy] != -1) continue; // 边界判断，以及去重判断
                ans[xx][yy] = temp.step + 1;
                que.push(node{xx, yy, temp.step + 1});
            }
        }
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                j != 1 && cout << " ";
                cout << ans[i][j];
            }
            cout << endl;
        }
        return 0;
    }
    ```

-   #### [HZOJ401.奇怪的象棋游戏升级版](http://oj.haizeix.com/problem/401)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    struct node {
        int x, y, step;
    };
    
    int n, x, y;
    int dir[12][2] = {1, 2, 1, -2, 2, 1, 2, -1, -1, 2, -1, -2, -2, 1, -2, -1, 2, 2, 2, -2, -2, 2, -2, -2}; // 12种方向
    int ans[505][505]; // 去重
    
    int main() {
        queue<node> que;
        que.push(node{1, 1, 0});
        ans[1][1] = -1; // 不能设置为0，否则无法去重
        while (!que.empty()) {
            node temp = que.front();
            que.pop();
            for (int i = 0; i < 12; i++) {
                int xx = temp.x + dir[i][0];
                int yy = temp.y + dir[i][1];
                if (xx < 1 || yy < 1 || xx > 500 || yy > 500 || ans[xx][yy] != 0) continue;
                ans[xx][yy] = temp.step + 1;
                que.push(node{xx, yy, temp.step + 1});
            }
        }
        cin >> n;
        for (int i = 0; i < n; i++) {
            cin >> x >> y;
            cout << ans[x][y] << endl;
        }
        return 0;
    }
    ```

-   #### [HZOJ303.矩阵距离一](http://oj.haizeix.com/problem/303)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    struct node {
        int x, y , step;
    };
    
    int n, m;
    int dir[4][2] = {1, 0, 0, 1, -1, 0, 0, -1};
    int ans[1005][1005];
    char mmap[1005][1005];
    queue<node> que;
    
    int main() {
        cin >> n >> m;
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                cin >> mmap[i][j];
                if (mmap[i][j] == '1') { // 以值为1的点往外扩展
                    ans[i][j] = -1; // 方便后面的去重
                    que.push(node{i, j, 0}); // 将所有的起点压入队列
                }
            }
        }
        while (!que.empty()) {
            node temp = que.front();
            que.pop();
            for (int i = 0; i < 4; i++) {
                int xx = temp.x + dir[i][0];
                int yy = temp.y + dir[i][1];
                if (xx < 1 || yy < 1 || xx > n || yy > m || ans[xx][yy] != 0) continue;
                ans[xx][yy] = temp.step + 1;
                que.push(node{xx, yy, temp.step + 1});
            }
        }
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                j != 1 && cout << " ";
                if (ans[i][j] == -1) cout << 0;
                else cout << ans[i][j];
            }
            cout << endl;
        }
        return 0;
    }
    ```

-   #### [HZOJ529.龙与虫](http://oj.haizeix.com/problem/529)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    /*
     * 思路：
     *  1、以终点为起点向八个方向进行延长，能够到达的位置check值标记为2，都是终点
     *  2、以虫的位置为起点广搜遍历，遇到2即可消灭敌人
     *  3、由于多组测试数据，不能改原地图，需要使用标记数组，1代表广搜遍历过了，2代表终点，0代表越界
     */
    
    struct node {
        int x, y, step;
    };
    
    int n, m, check[150][150];
    int dir[8][2] = {1, 0, -1, 0, 0, 1, 0, -1, 1, 1, 1, -1, -1, 1, -1, -1};
    char mmap[150][150];
    
    void init(int x, int y) { // 以敌人为起点向8个方向延长，将所经过点标记为终点
        check[x][y] = 2; // 先将敌人标记为终点
        for (int i = 0; i < 8; i++) { // 遍历八个方向
            for (int j = 1; 1; j++) { // 延长的深度
                int xx = x + dir[i][0] * j; 
                int yy = y + dir[i][1] * j;
                if (mmap[xx][yy] != 'O') break; // 越界或者遇到障碍物这个方向就没必要继续往下延长了
                check[xx][yy] = 2; // 延长遍历到的位置标记为终点
            }
        }
    }
    
    void bfs(int x, int y) { // 以虫为起点，广搜找到终点
        if (check[x][y] == 2) { // 起点即是终点
            cout << 0 << endl;
            return ;
        }
        queue<node> que;
        que.push(node{x, y, 0});
        while (!que.empty()) {
            node temp = que.front();
            que.pop();
            for (int i = 0; i < 4; i++) {
                int xx = temp.x + dir[i][0];
                int yy = temp.y + dir[i][1];
                if (check[xx][yy] == 2) { // 遇到终点就结束
                    cout << temp.step + 1 << endl;
                    return ;
                }
                if (mmap[xx][yy] == 'O' && check[xx][yy] == 0) { // 地图上此位置可遍历并且没有被遍历过
                    check[xx][yy] = 1; // 标记为已遍历
                    que.push(node{xx, yy, temp.step + 1});
                }
            }
        }
        cout << "Impossible!" << endl;
        return ;
    }
    
    int func() {
        int a, b, c, d;
        cin >> a >> b >> c >> d;
        if (a == 0) return 0;
        memset(check, 0, sizeof(check)); // 多次遍历，需要重置标记数组
        init(a, b); // 初始化终点
        bfs(c, d); // 广搜找结果
        return 1;
    }
    
    int main() {
        cin >> n >> m;
        for (int i = 1; i <= n; i++) {
            cin >> &mmap[i][1];
        }
        while (func()) {}
        return 0;
    }
    ```

-   #### [HZOJ81.小明回家](http://oj.haizeix.com/problem/81)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    /*
     * 思路：
     *  1、因为需要来回走，不能更改地图，需要使用标记数组check，0代表没有走过，1代表没手机走过，2表示有手机走过，3表示有手机和没手机都走过
     * 使用flag用来标记该点是有手机走过还是没有手机走过
     * 去重操作
     *
     */
    
    struct node {
        int x, y, step, flag;
    };
    
    int n, m, check[2005][2005];
    int dir[4][2] = {0, 1, 0, -1, 1, 0, -1, 0};
    char mmap[2005][2005];
    
    int main() {
        cin >> n >> m;
        queue<node> que;
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                cin >> mmap[i][j];
                if (mmap[i][j] == 'S') {
                    que.push(node{i, j, 0, 0}); // 将起点压入队列中
                    check[i][j] = 1;
                }
            }
        }
        while (!que.empty()) {
            node temp = que.front();
            que.pop();
            for (int i = 0; i < 4; i++) {
                int xx = temp.x + dir[i][0];
                int yy = temp.y + dir[i][1];
                if (mmap[xx][yy] == 'T' && temp.flag == 1) { // 如果有手机并遇到重点，结束
                    cout << temp.step + 1 << endl;
                    return 0;
                }
                if (((check[xx][yy] & 1) && temp.flag == 0) || ((check[xx][yy] & 2) && temp.flag == 1)) continue; // 避免没手机时 重复走过某一点，或者 有手机时重复走过某一点
                if (mmap[xx][yy] == '.' || mmap[xx][yy] == 'S' || mmap[xx][yy] == 'T') { //
                    check[xx][yy] += temp.flag + 1; //
                    que.push(node{xx, yy, temp.step + 1, temp.flag});
                }
                if (mmap[xx][yy] == 'P') { // 经过手机店
                    check[xx][yy] = 3;
                    que.push(node{xx, yy, temp.step + 1, 1});
                }
            }
        }
        return 0;
    }
    ```

-   #### [HZOJ527.飞跃原野](http://oj.haizeix.com/problem/527)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    /*
     * 思路：将飞和走两种情况都遍历一遍，先遍历飞的情况，因为飞一次可以是好几步，但只看作一次
     * 去重：check[x][y][d]，d代表在当前能量下是否访问过该位置
     */
    
    struct node {
        int x, y, step, d;
    };
    
    int m, n, d, check[105][105][105];
    int dir[4][2] = {0, 1, 0, -1, 1, 0, -1, 0};
    char mmap[105][105];
    
    int main() {
        cin >> m >> n >> d;
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                cin >> mmap[i][j];
            }
        }
        queue<node> que;
        que.push(node{1, 1, 0, d});
        for (int i = 1; i <= d; i++) { // 对于起点来说，不管是否有能量，都尽量不要再走一遍起点
            check[1][1][i] = 1;
        }
        while (!que.empty()) {
            node temp = que.front();
            que.pop();
            for (int i = 0; i < 4; i++) {
                for (int j = 2; j <= temp.d; j++) { // 先飞，飞的距离受能量的影响
                    int xx = temp.x + dir[i][0] * j;
                    int yy = temp.y + dir[i][1] * j;
                    if (mmap[xx][yy] == 0) break; // 越界直接终止该方向后面的尝试
                    if (xx == m && yy == n) { // 飞到终点上
                        cout << temp.step + 1 << endl;
                        return 0;
                    }
                    if (mmap[xx][yy] == 'P' && check[xx][yy][temp.d - j] == 0) { // 飞到平地上，并且在当前能量下之前没经过
                        check[xx][yy][temp.d - j] = 1;
                        que.push(node{xx, yy, temp.step + 1, temp.d - j});
                    }
                }
                int xx = temp.x + dir[i][0]; // 进行走的遍历
                int yy = temp.y + dir[i][1];
                if (xx == m && yy == n) { // 走到终点上了
                    cout << temp.step + 1 << endl;
                    return 0;
                }
                if (mmap[xx][yy] == 'P' && check[xx][yy][temp.d] == 0) { // 走不需要能耗
                    check[xx][yy][temp.d] = 1;
                    que.push(node{xx, yy , temp.step + 1, temp.d});
                }
            }
        }
        cout << "impossible" << endl;
    
        return 0;
    };
    ```

-   #### [HZOJ402.奇怪的电梯](http://oj.haizeix.com/problem/402)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    /*
     * 思路：
     *  之前存的是当前位置x,y值，此处直接存楼层now，以及最小步数step
     *  方向数组不需要，因为只有两个方向，要不然上，要不然下
     */
    
    struct node {
        int now, step;
    };
    
    int n, a, b, num[205], mark[205];
    
    int main() {
        cin >> n >> a >> b;
        for (int i = 1; i <= n; i++) {
            cin >> num[i];
        }
        queue<node> que;
        que.push(node{a, 0}); // 先将起点压入队列
        while (!que.empty()) {
            node temp = que.front();
            que.pop();
            if (temp.now == b) { // 如果当前位置就是目标点，则直接结束遍历
                cout << temp.step << endl;
                return 0;
            }
            int xx = temp.now + num[temp.now]; // 向下
            int yy = temp.now - num[temp.now]; // 向上
            if (xx <= n && mark[xx] == 0) { // 向下运行
                mark[xx] = 1;
                que.push(node{xx, temp.step + 1});
            }
            if (yy >= 1 && mark[yy] == 0) { // 向上运行
                mark[yy] = 1;
                que.push(node{yy, temp.step + 1});
            }
        }
        cout << "-1" << endl;
    
        return 0;
    }
    ```

-   #### [HZOJ530.警察找车](http://oj.haizeix.com/problem/530)

    ![image-20220116084229462](https://dongqing1903.oss-cn-shanghai.aliyuncs.com/img/image-20220116084229462.png)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    struct node {
        int x, y;
    };
    
    int n, m, k, sx, sy, dir_num, mark[55][55];
    int dir[4][2] = {-1, 0, 1, 0, 0, -1, 0, 1};
    string str;
    char mmap[55][55];
    queue<node> que;
    
    int main() {
        cin >> n >> m;
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                cin >> mmap[i][j];
                if (mmap[i][j] == '*') { // 记录起点，并把起点置为.，因为起点后面也有可能遍历到
                    sx = i;
                    sy = j;
                    mmap[i][j] = '.'; //
                }
            }
        }
        que.push(node{sx, sy});
        cin >> k;
        while (k--) {
            cin >> str;
            if (str == "NORTH") dir_num = 0;
            if (str == "SOUTH") dir_num = 1;
            if (str == "WEST") dir_num = 2;
            if (str == "EAST") dir_num = 3;
            memset(mark, 0, sizeof(mark)); // 每一层都需要单独标记，同一个位置可以通过不同的方向走法经过，但同一方向时只允许经过一次
            int cnt = que.size(); // 需要记录当前的队列元素数量，要不然使用que.size是一直变动的
            for (int i = 0; i < cnt; i++) {
                node temp = que.front();
                que.pop();
                for (int j = 1; 1; j++) {
                    int xx = temp.x + dir[dir_num][0] * j;
                    int yy = temp.y + dir[dir_num][1] * j;
                    if (mmap[xx][yy] != '.') break;
                    if (mark[xx][yy] == 0) {
                        mark[xx][yy] = 1;
                        que.push(node{xx, yy});
                    }
                }
            }
        }
    
        while (!que.empty()) { // 到达树的最后一行
            node temp = que.front();
            que.pop();
            mmap[temp.x][temp.y] = '*';
        }
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                cout << mmap[i][j];
            }
            cout << endl;
        }
        return 0;
    }
    ```

-   启发式广度搜索

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    #include <cmath>
    using namespace std;
    /*
     * 思路：对于每一个方向的遍历都要优先考虑权值大的方向，这样就可以避免不必要的搜索
     * 权值：该位置与终点的距离越近越好，使用欧式距
     * 方法：使用优先队列按照欧氏距离的大小进行排序
     * 优点：较少非必要的的搜索过程
     */
    struct node {
        int x, y, step;
        double h; // 欧式距
        bool operator<(const node &b) const { // priority_queue重载的是<，这里需要反着写
            return step + h > b.step + b.h;
        }
    };
    
    int n, m, sx, sy, ex, ey;
    int dir[8][2] = {0, 1, 0, -1, 1, 0, -1, 0, 1, 1, 1, -1, -1, 1, -1, -1}; // 8个方向遍历
    char mmap[105][105];
    double dis(int x, int y) { // 求欧式距
        int t1 = x - ex, t2 = y - ey;
        return sqrt(t1 * t1 + t2 * t2);
    }
    
    void print(int cnt) { // 输出
        cout << "------------------" << cnt << "--------------------------" << endl;
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                cout << mmap[i][j];
                if (mmap[i][j] == 'x') {
                    mmap[i][j] = '~';
                }
            }
            cout << endl;
        }
        cout << "---------------------------------------------  ----------" << endl;
    }
    int main() {
        cin >> n >> m;
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                cin >> mmap[i][j];
                if (mmap[i][j] == 'S') { // 记录起点位置
                    sx = i, sy = j;
                }
                if (mmap[i][j] == 'T') { // 记录终点位置
                    ex = i, ey = j;
                }
            }
        }
        priority_queue<node> que; 
        que.push(node{sx, sy, 0, dis(sx, sy)}); // 先存入起点
        while (!que.empty()) {
            node temp = que.top();
            que.pop();
            int cnt = 0;
            for (int i = 0; i < 8; i++) {
                int xx = temp.x + dir[i][0];
                int yy = temp.y + dir[i][1];
                if (mmap[xx][yy] == 'T') { // 到达终点就结束搜索
                    cout << temp.step + 1 << endl;
                    print(cnt);
                    return 0;
                }
                if (mmap[xx][yy] == '.') {
                    mmap[xx][yy] = 'x'; // 去重
                    que.push(node{xx, yy, temp.step + 1, dis(xx, yy)});
                    cnt += 1;
                    if (cnt % 5 == 0) {
                        print(cnt);
                    }
                }
            }
        }
        cout << -1 << endl;
        return 0;
    }
    ```

>   **说明**：广搜主要解决最少步数问题，对于多次查询的题目，需要将整个地图的情况遍历完，类似于线下更新，线上查询的效果，这样可以再遍历时通过位置与位置之间的关系，减少时间

## 最短路

### 1、最短路的理论要点

1、负环

2、邻接矩阵的优缺点：图论算法1，用来解决floyd

3、连接表的优缺点：图论算法1，用来解决djksta

4、djksta：无负边  

5、链式前向星：

-   边集（e, v, next）:next表示同一起点的上一条边的边号
-   head数组：某一个点为起点的最后一条边的边号

6、Bellman-fold：通过边，用边起点的最短路+边的权值去更新终点的答案

7、基于队列的Bellman-fold算法的优化：只有上一轮更新过的点才能影响下一轮的答案

### 2、重点题型

-   #### [HZOJ746.最短路](http://oj.haizeix.com/problem/746)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    /*
     * floyed算法
     */
    
    int n, m, s;
    int arr[1005][1005];
    
    int main() {
        memset(arr, 0x3f, sizeof(arr)); // 
        cin >> n >> m >> s;
        int a, b, c;
        for (int i = 1; i <= m; i++) {
            cin >> a >> b >> c;
            if (arr[a][b] > c) { // 有重边，需要选权值最小的情况
                arr[a][b] = c;
                arr[b][a] = c;
            }
        }
        for (int i = 1; i <= n; i++) { // i作为中转
            for (int j = 1; j <= n; j++) {
                for (int k = 1; k <= n; k++) {
                    arr[j][k] = min(arr[j][k], arr[j][i] + arr[i][k]);
                }
            }
        }
        for (int i = 1; i <= n; i++) {
            arr[i][i] = 0;
            if (arr[s][i] == 0x3f3f3f3f) cout << -1 << endl;
            else cout << arr[s][i] << endl;
        }
        return 0;
    }
    ```

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    /*
     * djksta
     */
    struct node { // 算法过程中状态
        int now, dis; // 当前位置，从起点到达当前位置的长度
        bool operator<(const node &a) const {
            return this->dis > a.dis;
        }
    };
    
    struct edge{ // 连接表存边信息
        int e, v; // 终点和权值
    };
    
    int main() {
        int n, m, s, ans[100005];
        memset(ans, 0x3f, sizeof(ans));
        cin >> n >> m >> s;
        vector<vector<edge>> edg(n + 5);
        for (int i = 0; i < m; i++) {
            int a, b, c;
            cin >> a >> b >> c;
            edg[a].push_back(edge{b, c}); // 无向边两个方向都要存
            edg[b].push_back(edge{a, c});
        }
        priority_queue<node> que;
        que.push(node{s, 0}); // 从起点开始查找
        ans[s] = 0;
        while (!que.empty()) {
            node temp = que.top();
            que.pop();
            if (ans[temp.now] < temp.dis) continue; // 证明这个点已经走过了
            for (int i = 0; i < edg[temp.now].size(); i++) {
                int e = edg[temp.now][i].e, v = edg[temp.now][i].v;
                if (ans[e] > temp.dis + v) { // 更新最短路径
                    ans[e] = temp.dis + v;
                    que.push(node{e, ans[e]}); // 继续压入优先队列中
                }
            }
        }
        for (int i = 1; i <= n; i++) {
            if (ans[i] == 0x3f3f3f3f) cout << -1 << endl;
            else cout << ans[i] << endl;
        }
        return 0;
    }
    ```

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    /* 
     * 链式前向星 + djksta
     */
    struct edge { // 链式前向星的边信息
        int e, v, next; // e是终点，v是代价，next是同一起点的上一条边
    };
    
    struct node { // djksta的信息存储
        int now, dis; 
        bool operator<(const node &a) const {
            return this->dis > a.dis;
        }
    };
    edge edg[200005]; // 无向图，正方方向都要存储边，edg用来存储链式前向星的边信息
    int n, m, s, cnt, ans[100005], head[100005]; 
    void add_to_edge(int s, int e, int v) {  // 数据更新顺序不能变
        edg[cnt].e = e; 
        edg[cnt].v = v;
        edg[cnt].next = head[s]; // head记录的是当前i为起点的最后一条边的终点
        head[s] = cnt; // 此时head已经变了
        cnt += 1; // 因为是无向图，需要记录两条边的信息。使用cnt帮助记录边情况
    }
    
    int main() {
        memset(head, -1, sizeof(head));
        memset(ans, 0x3f, sizeof(ans));
        cin >> n >> m >> s;
        for (int i = 0; i < m; i++) {
            int a, b, c;
            cin >> a >> b >> c;
            add_to_edge(a, b, c);
            add_to_edge(b, a, c);
        }
        priority_queue<node> que;
        que.push(node{s, 0});
        ans[s] = 0; // 标记起点
        while (!que.empty()) {
            node temp = que.top();
            que.pop();
            if (ans[temp.now] < temp.dis) continue;
            for (int i = head[temp.now]; i != -1; i = edg[i].next) { // 像链表的遍历方式
                int e = edg[i].e, v = edg[i].v;
                if (ans[temp.now] + v < ans[e]) {
                    ans[e] = ans[temp.now] + v;
                    que.push(node{e, ans[e]});
                }
            }
        }
        for (int i = 1; i <= n; i++) {
            if (ans[i] == 0x3f3f3f3f) cout << "-1" << endl;
            else cout << ans[i] << endl;
        }
        return 0;
    } 
    ```

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    /*
     * Bellman-fold：单源、慢、负权
     */
    
    struct edge { // 边信息
        int s, e, v; // 起点，终点，权值
    };
    
    edge edg[20005];
    int n, m, s, cnt, ans[10005];
    
    void add_to_edge(int a, int b, int c) { // 存边
        edg[cnt].s = a;
        edg[cnt].e = b;
        edg[cnt].v = c;
        cnt += 1; 
    }
    
    int main() {
        memset(ans, 0x3f, sizeof(ans));
        cin >> n >> m >> s;
        for (int i = 0; i < m; i++) {
            int a, b, c;
            cin >> a >> b >> c;
            add_to_edge(a, b, c);
            add_to_edge(b, a, c);
        }
        ans[s] = 0; // 一定要初始化起点
        int f = 0;
        for (int i = 0; i < n; i++) { // 遍历循环的次数
            f = 0;
            for (int j = 0; j < cnt; j++) { // 遍历所有的边
                if (ans[edg[j].e] > ans[edg[j].s] + edg[j].v) { // 更新结果
                    ans[edg[j].e] = ans[edg[j].s] + edg[j].v;
                    f = 1;
                }
            }
            if (f == 0) break;
        }
        for (int i = 1; i <= n; i++) {
            if (ans[i] == 0x3f3f3f3f) cout << "-1" << endl;
            else cout << ans[i] << endl;
        }
        return 0;
    }
    ```

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    /*
     * 基于队列优化的Bellman-fold算法：更新特定起点的最短路，需要采用邻接表或链式前向星存储边的信息
     */
    struct edge { 
        int e, v, next;
    };
    
    edge edg[20005];
    int n, m, s, cnt, head[10005], ans[10005], mark[10005]; // mark用来每一轮去重用的，同一个对于一轮来说，只需要放入队列一次
    queue<int> que;
    
    void add_to_edge(int a, int b, int c) {
        edg[cnt].e = b;
        edg[cnt].v = c;
        edg[cnt].next = head[a];
        head[a] = cnt;
        cnt += 1;
    }
    
    int main() {
        memset(ans, 0x3f, sizeof(ans));
        memset(head, -1, sizeof(head));
        cin >> n >> m >> s;
        for (int i = 0; i < m; i++) {
            int a, b, c;
            cin >> a >> b >> c;
            add_to_edge(a, b, c);
            add_to_edge(b, a, c);
        }
        que.push(s);
        ans[s] = 0;
        mark[s] = 1; // 第一轮，将起点标记为已经添加队列了
        while (!que.empty()) {
            int temp = que.front();
            que.pop();
            mark[temp] = 0; // 后面有可能继续更新  ********************
            for (int i = head[temp]; i != -1; i = edg[i].next) {
                int e = edg[i].e, v = edg[i].v;
                if (ans[e] > ans[temp] + v) {
                    ans[e] =  ans[temp] + v;
                    if (mark[e] == 0) { // 只有没添加过的点才可以添加
                        que.push(e);
                        mark[e] = 1;
                    }
                }
            }
        }
        for (int i = 1; i <= n; i++) {
            if (ans[i] == 0x3f3f3f3f) cout << "-1" << endl;
            else cout << ans[i] << endl;
        }
    	return 0;
    }
    ```

-   #### [LUOGU.P1364.医院设置](https://www.luogu.com.cn/problem/P1364)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    int n, arr[105][105], num[105];
    
    int main() {
        memset(arr, 0x3f, sizeof(arr));
        cin >> n;
        for (int i = 1; i <= n; i++) {
            cin >> num[i];
            int a, b;
            cin >> a >> b;
            if (a) {
                arr[i][a] = 1;
                arr[a][i] = 1;
            }
            if (b) {
                arr[i][b] = 1;
                arr[b][i] = 1;
            }
            arr[i][i] = 0; // 把对角线上的元素初始化为0
        }
        for (int i = 1; i <= n; i++) { // 先用floyd算法，找到最短路
            for (int j = 1; j <= n; j++) {
                for (int k = 1; k <= n; k++) {
                    arr[j][k] = min(arr[j][k], arr[j][i] + arr[i][k]);
                }
            }
        }
        int ans = 0x3f3f3f3f;
        for (int i = 1; i <= n; i++) {
            int t = 0;
            for (int j = 1; j <= n; j++) {
                t += arr[i][j] * num[j];
            }
            ans = min(ans, t);
        }
        cout << ans << endl;
        return 0;
    }
    ```

-   #### [LUOGU.P1119.灾后重建](https://www.luogu.com.cn/problem/P1119)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    /*
     * 思想：多源最短路问题，采用floyd算法
     * 由题意知，floyd算法的中间转折点i是需要在时间允许的条件下才能作为周转点，也就是说要把floyd的第一层循环给拆解开
     */
    int n, m, q, now, num[205], arr[205][205]; // now是当前修改到了哪一个点
    
    int main() {
        memset(arr, 0x3f, sizeof(arr));
        cin >> n >> m;
        for (int i = 0; i < n; i++) { // 存储每个村庄所需的修复时间
            cin >> num[i];
            arr[i][i] = 0;
        }
        for (int i = 0; i < m; i++) { // 存储边的信息
            int a, b, c;
            cin >> a >> b >> c;
            arr[a][b] = c;
            arr[b][a] = c;
        }
        cin >> q;
        for (int i = 0; i < q; i++) {
            int x, y, t;
            cin >> x >> y >> t;
            while (num[now] <= t && now < n) { // 拆解第一层循环i
                for (int j = 0; j < n; j++) {
                    for (int k = 0; k < n; k++) {
                        arr[j][k] = min(arr[j][k], arr[j][now] + arr[now][k]);
                    }
                }
                now += 1;
            }
            if (num[x] > t || num[y] > t || arr[x][y] == 0x3f3f3f3f) cout << -1 << endl; // 需要特殊处理，特殊考虑
            else cout << arr[x][y] << endl;
        }
        return 0;
    }
    ```

-   #### [LUOGU.P1629.邮递员送信](https://www.luogu.com.cn/problem/P1629)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    /*
     * 思路：
     *	1、从节点1出发时是单源最短路
     * 	2、从各节点返回到节点1时，是多源最短路问题，需要用到floyd算法，但容易超时，可以转换思想，从A到B，等价于从B到A，这里可以等价于从节点1到各节点
     * 总结：使用基于队列优化的Bellman—flod算法进行单源最短路的求解
     */
    struct edge { // 链式前向星的边集定义
        int e, v, next;
    };
    
    int n, m, ans[2][1005], head[2][1005], mark[10005]; // 由于考虑到去和回来，需要存两次，这里开一个二维数组进行存储，而mark标记数组可以在每一轮进行初始化
    edge edg[2][100005];
    
    void add_to_edge(int cnt, int index, int s, int e, int v) {
        edg[cnt][index].e = e;
        edg[cnt][index].v = v;
        edg[cnt][index].next = head[cnt][s];
        head[cnt][s] = index;
    }
    
    void func(int cnt) { // Bellman-fold算法
        memset(mark, 0, sizeof(mark)); // 每一轮都需要初始化
        queue<int> que;
        que.push(1);
        ans[cnt][1] = 0; // 添加起点
        mark[1] = 1;
        while (!que.empty()) {
            int temp = que.front();
            que.pop();
            mark[temp] = 0; // ******** 下一轮有可能继续更新，这里要解放当前位置
            for (int i = head[cnt][temp]; i != -1; i = edg[cnt][i].next) {
                int e = edg[cnt][i].e, v = edg[cnt][i].v;
                if (ans[cnt][e] > ans[cnt][temp] + v) {
                    ans[cnt][e] = ans[cnt][temp] + v;
                    if (mark[e] == 0) {
                        que.push(e);
                        mark[e] = 1;
                    }
                }
            }
        }
    }
    
    int main() {
        memset(ans, 0x3f, sizeof(ans));
        memset(head, -1, sizeof(head));
        cin >> n >> m;
        for (int i = 0; i < m; i++) {
            int a, b, c;
            cin >> a >> b >> c;
            add_to_edge(0, i, a, b, c); // 存两次边信息
            add_to_edge(1, i, b, a, c);
        }
        func(0);
        func(1);
        long long sum = 0;
        for (int i = 1; i <= n; i++) {
            sum += ans[0][i] + ans[1][i]; // 计算总值
        }
        cout << sum << endl;
        return 0;
    }
    ```

-   #### [LUOGU.P2865.USACO06NOV\Roadblocks G](https://www.luogu.com.cn/problem/P2865)

    ```c++
    #include <iostream>
    #include <cstdio>
#include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    /*
     * 思想：单源最短路问题
     * 更新情况：
     *		1、最短路更新最短路
     *		2、最短路更新次短路
     *		3、次短路更新次短路
     */
    
    struct edge {
        int e, v, next;
    };
    
    int n, m, cnt, ans[5005], ans2[5005], head[5005], mark[5005];
    edge edg[200005];
    
    void add_to_edge(int a, int b, int c) {
        edg[cnt].e = b;
        edg[cnt].v = c;
        edg[cnt].next = head[a];
        head[a] = cnt;
        cnt += 1;
    }
    
    int main() {
        memset(ans, 0x3f, sizeof(ans));
        memset(ans2, 0x3f, sizeof(ans2));
        memset(head, -1, sizeof(head));
        cin >> n >> m;
        for (int i = 0; i < m; i++) {
            int a, b, c;
            cin >> a >> b >> c;
            add_to_edge(a, b, c);
            add_to_edge(b, a, c);
            if (a == 1 || b == 1) { // 记录起点的次短路，就是与1相连的最短距离的来回
                ans2[1] = min(ans2[1], c * 2);
            }
        }
        queue<int> que;
        que.push(1);
        mark[1] = 1;
        ans[1] = 0;
        while (!que.empty()) {
            int temp = que.front();
            que.pop();
            mark[temp] = 0;
            for (int i = head[temp]; i != -1; i = edg[i].next) { 
                int e = edg[i].e, v = edg[i].v;
                if (ans[e] > ans[temp] + v) { // 最短路更新最短路
                    ans2[e] = ans[e]; // 被更新下来的最短路就变为次短路
                    ans[e] = ans[temp] + v;
                    if (mark[e] == 0) {
                        mark[e] = 1;
                        que.push(e);
                    }
                }
                if (ans[temp] + v < ans2[e] && ans[temp] + v != ans[e]) { // 最短路更新次短路
                    ans2[e] = ans[temp] + v;
                    if (mark[e] == 0) {
                        mark[e] = 1;
                        que.push(e);
                    }
                }
                if (ans2[temp] + v < ans2[e]) { // 次短路更新次短路
                    ans2[e] = ans2[temp] + v;
                    if (mark[e] == 0) {
                        mark[e] = 1;
                        que.push(e);
                    }
                }
            }
        }
        cout << ans2[n] << endl;
        return 0;
    }
    ```
    

## 最小生成树

### 1、最小生成树的理论要点

边数=点数-1

2:04的位置

### 2、重点题型

-   #### [LUOGU.P3366.最小生成树](https://www.luogu.com.cn/problem/P3366)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    /*
     * krusal算法：利用边信息，将边按照边权进行升序排列，利用并查集+最小边权进行建立最小生成树
     */
    struct edge { // 存放边信息
        int s, e, v;
        bool operator<(const edge &a) const {
            return this->v < a.v;
        }
    };
    
    edge edg[100005];
    int n, m, ans, my_union[5005], cnt;
    
    void init() { // 初始化并查集
        for (int i = 1; i <= n; i++) {
            my_union[i] = i;
        }
    }
    
    int my_find(int x) { // 并查集查找父节点 + 路径优化
        if (my_union[x] == x) {
            return x;
        }
        return my_union[x] = my_find(my_union[x]); // 路径压缩
    }
    
    int main() {
        cin >> n >> m;
        for (int i = 0; i < m; i++) {
            cin >> edg[i].s >> edg[i].e >> edg[i].v;
        }
        sort(edg, edg + m); // 按边权进行排序
        init(); // 初始化并查集
        for (int i = 0; i < m; i++) {
            int s = edg[i].s, e = edg[i].e, v = edg[i].v;
            int fa = my_find(s), fb = my_find(e); // 找到起点和终点的父节点
            if (fa != fb) { 
                my_union[fa] = fb; // 合并
                ans += v;
                cnt += 1; // cnt来判断是否已经构成最小生成树
                if (cnt == n - 1) {
                    cout << ans << endl;
                    return 0;
                }
    
            }
        }
        cout << "orz" << endl;
        return 0;
    }
    ```

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    using namespace std;
    
    /*
     * prim算法：利用点的信息，利用优先队列将已经遍历到的点所连接的边进行排序，每次选择最小代价的边进行延申
     */
    struct node { // 放在优先队列中打包排序用
        int e, v;
        bool operator<(const node &a) const {
            return this->v > a.v;
        }
    };
    
    struct edge { // 链式前向星用来存边信息
        int e, v, next;
    };
    /*
     * edge_cnt用来记录边信息
     * cnt用来记录已经生成最小数的边数
     * mark[]用来去重
     * num[x]用来记录连接x的边的权值
     */
    int n, m, cnt, edge_cnt, ans, head[5005], mark[5005], num[5005]; 
    edge edg[400005];
    
    void add_to_edge(int a, int b, int c) {
        edg[edge_cnt].e = b;
        edg[edge_cnt].v = c;
        edg[edge_cnt].next = head[a];
        head[a] = edge_cnt++;
    }
    
    int main() {
        memset(head, -1, sizeof(head));
        memset(num, 0x3f, sizeof(num));
        cin >> n >> m;
        for (int i = 0; i < m; i++) {
            int a, b, c;
            cin >> a >> b >> c;
            add_to_edge(a, b, c);
            add_to_edge(b, a, c);
        }
        priority_queue<node> que;
        que.push(node{n / 2, 0}); // 以任一点为起点
        while (!que.empty()) {
            node temp = que.top();
            que.pop();
            if (mark[temp.e] == 1) continue; // 已经访问过了
            ans += temp.v; 
            mark[temp.e] = 1;
            cnt += 1;
            if (cnt == n) { // 最小生成树已经生成
                cout << ans << endl;
                return 0;
            }
            for (int i = head[temp.e]; i != -1; i = edg[i].next) {
                int e = edg[i].e, v = edg[i].v;
                if (mark[e] == 0 && num[e] > v) { // 这里num加不加都可以，是用来优化代码的
                    que.push(node{e, v});
                    num[e] = v;
                }
            }
        }
        cout << "orz" << endl;
        return 0;
    }
    ```

-   #### [LUOGU.P1265.公路修建](https://www.luogu.com.cn/problem/P1265)

    ```c++
    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <stack>
    #include <set>
    #include <map>
    #include <string>
    #include <algorithm>
    #include <memory>
    #include <cstring>
    #include <cmath>
    using namespace std;
    
    /*
     * 思路：用prim算法，因为本题数据量较大
     */
    
    struct node {
        int e;
        double v;
        bool operator<(const node &a) const {
            return this->v > a.v;
        }
    };
    int n, m, xy[5005][2], mark[5005], cnt;
    double num[5005], ans;
    
    double func(int a, int b) {
        long long t1 = xy[a][0] - xy[b][0];
        long long t2 = xy[a][1] - xy[b][1];
        return sqrt(t1 * t1 + t2 * t2);
    }
    
    
    int main() {
        cin >> n;
        for (int i = 1; i <= n; i++) {
            cin >> xy[i][0] >> xy[i][1]; // 因为只提供城市的坐标，对于城市间的距离需要通过计算得到
            num[i] = 999999999; // 为了后面优化使用，对于double不能使用memset，只能一个一个的初始化
        }
        priority_queue<node> que;
        que.push(node{1, 0});
        while (!que.empty()) {
            node temp = que.top();
            que.pop();
            if (mark[temp.e] == 1) continue;
            ans += temp.v;
            cnt += 1;
            mark[temp.e] = 1;
            if (cnt == n) {
                printf("%.2f\n", ans);
                return 0;
            }
            for (int i = 1; i <= n; i++) {
                if (mark[i] == 0 && i != temp.e) {
                    double t = func(temp.e, i); // 计算两点之间的距离
                    if (num[i] > t) { // 算法优化
                        num[i] = t;
                        que.push(node{i, t});
                    }
                }
            }
        }
        return 0;
    }
    ```

-   #### [LUOGU.P1991.无线通讯网](https://www.luogu.com.cn/problem/P1991)

    ```c++
    
    ```

-   #### [LUOGU.P1144.最短路计数](https://www.luogu.com.cn/problem/P1144)

    ```c++
    
    ```

## 拓扑排序

### 1、拓扑排序的理论要点





### 2、重点题型

-   #### [HZOJ641.拓扑排序](http://oj.haizeix.com/problem/641)

    ```c++
    
    ```

-   #### [HZOJ635.神经网络](http://oj.haizeix.com/problem/635)

    ```c++
    
    ```

-   #### [HZOJ636.旅游计划](http://oj.haizeix.com/problem/636)

    ```c++
    
    ```

    

-   #### [HZOJ640.食物链计数](http://oj.haizeix.com/problem/640)

    ```c++
    
    ```

-   #### [HZOJ637.排序](http://oj.haizeix.com/problem/637)

    ```c++
    
    ```

-   #### [HZOJ638.最长路](http://oj.haizeix.com/problem/638)

    ```c++
    
    ```

-   #### [HZOJ674.宿舍楼里的电竞赛](http://oj.haizeix.com/problem/674)

    ```c++
    
    ```

    


## 单调栈

### 1、单调栈的理论要点

-   **单调栈的本质**：空间换时间，因为在遍历的过程中需要用一个栈来记录右边第一个比当前元素的元素，优点是只需要遍历一次
-   **单调栈里存放的元素**：单调栈里只需要存放元素的下标i就可以了，如果需要使用对应的元素，直接T[i]就可以获取
-   **单调栈里元素是递增呢？ 还是递减呢？**：递增循序（再强调一下是指从栈头到栈底的顺序），因为只有递增的时候，加入一个元素i，才知道栈顶元素在数组中右面第一个比栈顶元素`大`的元素是i；递减栈就是求右边第一个比自己`小`的元素

### 2、重点题型

-   #### [LC739.每日温度](https://leetcode-cn.com/problems/daily-temperatures/)

    ```c++
    class Solution {
    public:
        vector<int> dailyTemperatures(vector<int>& t) {
            stack<int> sta;
            vector<int> res(t.size(), 0); // 记录结果
            sta.push(0);
            for (int i = 1; i < t.size(); i++) {
                if (t[i] < t[sta.top()]) sta.push(i); // 情况一：当前遍历的元素T[i]小于栈顶元素T[st.top()]的情况
                else if (t[i] == t[sta.top]) sta.push(i); // 情况二：当前遍历的元素T[i]等于栈顶元素T[st.top()]的情况
                else { // 情况三：当前遍历的元素T[i]大于栈顶元素T[st.top()]的情况
                    while (!sta.empty() && t[i] > t[sta.top()]) {
                        res[sta.top()] = i - sta.top();
                        sta.pop();
                    }
                }
            }
            return res;
        }
    };
    ```

    ```c++
    class Solution {
    public:
        vector<int> dailyTemperatures(vector<int>& t) {
            stack<int> sta;
            vector<int> res(t.size(), 0);
            sta.push(0);
            for (int i = 1; i < t.size(); i++) {
                while (!sta.empty() && t[i] > t[sta.top()]) {
                    res[sta.top()] = i - sta.top();
                    sta.pop();
                }
                sta.push(i);
            }
            return res;
        }
    };
    ```

-   #### [LC496.下一个更大元素 I](https://leetcode-cn.com/problems/next-greater-element-i/)

    ```c++
    class Solution {
    public:
        vector<int> nextGreaterElement(vector<int>& nums1, vector<int>& nums2) {
            stack<int> sta;
            vector<int> result(nums1.size(), -1);
            if (nums1.size() == 0) return result;
    
            unordered_map<int, int> umap;
            for (int i = 0; i < nums1.size(); i++) umap[nums1[i]] = i;
            sta.push(0);
            for (int i = 1; i < nums2.size(); i++) {
                if (nums2[i] < nums2[sta.top()]) sta.push(i);
                else if (nums2[i] == nums2[sta.top()]) sta.push(i);
                else {
                    while (!sta.empty() && nums2[i] > nums2[sta.top()]) {
                        if (umap.find(nums2[sta.top()]) != umap.end()) {
                            result[umap[nums2[sta.top()]]] = nums2[i];
                        }
                        sta.pop();
                    }
                    sta.push(i);
                }
            }
            return result;
        }
    };
    ```

-   #### [LC503.下一个更大元素 II](https://leetcode-cn.com/problems/next-greater-element-ii/)

    ```c++
    class Solution {
    public:
        vector<int> nextGreaterElements(vector<int>& nums) {
            vector<int> res(nums.size(), -1);
            if (nums.size() == 0) return res;
            stack<int> sta;
            sta.push(0);
            for (int i = 0; i < nums.size() * 2; i++) { // 两次遍历数组，相当于循环遍历了
                if (nums[i % nums.size()] < nums[sta.top()]) sta.push(i % nums.size());
                else if (nums[i % nums.size()] == nums[sta.top()]) sta.push(i % nums.size());
                else {
                    while (!sta.empty() && nums[i % nums.size()] > nums[sta.top()]) {
                        res[sta.top()] = nums[i % nums.size()];
                        sta.pop();
                    }
                    sta.push(i % nums.size());
                }
            }
            return res;       
        }
    };
    ```

-   #### [LC42.接雨水](https://leetcode-cn.com/problems/trapping-rain-water/)

    ```c++
    class Solution {
    public:
        int trap(vector<int>& height) {
            // 双指针法
            int sum = 0;
            for (int i = 0; i < height.size(); i++) {
                // 第一个柱子和最后一个柱子不接雨水
                if (i == 0 || i == height.size() - 1) continue;
                int rHeight = height[i]; // 左侧最高的柱子
                int lHeight = height[i]; // 右侧最高的柱子
                for (int j = i + 1; j < height.size(); j++) rHeight = max(rHeight, height[j]); // 寻找当前位置右侧最高的柱子：存在重复计算，导致超时
                for (int j = i - 1; j >= 0; j--) lHeight = max(lHeight, height[j]); // 寻找当前位置左侧最高的柱子
                int h = min(rHeight, lHeight) - height[i]; // 当前柱子可接的雨水量
                if (h > 0) sum += h;
            }
            return sum;
        }
    };
    ```

    ```c++
    class Solution {
    public:
        int trap(vector<int>& height) {
            // 利用动态规划分别记录下来每一个位置最左最高和最右最高的情况
            vector<int> maxLeft(height.size());
            vector<int> maxRight(height.size());
            maxLeft[0] = height[0];
            for (int i = 1; i < height.size(); i++) maxLeft[i] = max(maxLeft[i - 1], height[i]); // 最左最高
            maxRight[height.size() - 1] = height[height.size() - 1];
            for (int i = height.size() - 2; i >= 0; i--) maxRight[i] = max(maxRight[i + 1], height[i]); // 最右最高
            int sum = 0;
            for (int i = 0; i < height.size(); i++) {
                int count = min(maxLeft[i], maxRight[i]) - height[i]; // 取左右的最矮的
                if (count > 0) sum += count; 
            }
            return sum;
        }
    };
    ```

    ```c++
    class Solution {
    public:
        int trap(vector<int>& height) {
            stack<int> sta; // 单调递增栈，存储下标值
            sta.push(0); 
            int sum = 0;
            for (int i = 1; i < height.size(); i++) {
                if (height[i] < height[sta.top()]) sta.push(i); // 当前遍历的元素（柱子）高度小于栈顶元素的高度，就把这个元素加入栈中，因为栈里本来就要保持从小到大的顺序（从栈头到栈底）
                else if (height[i] == height[sta.top()]) { // 前遍历的元素（柱子）高度等于栈顶元素的高度，要跟更新栈顶元素，因为遇到相相同高度的柱子，需要使用最右边的柱子来计算宽度
                    sta.pop();
                    sta.push(i);
                } else { // 当前遍历的元素（柱子）高度大于栈顶元素的高度，此时就出现凹槽了
                    while (!sta.empty() && height[i] > height[sta.top()]) { // 需要循环操作，类似于75536的情况
                        // 栈顶和栈顶的下一个元素以及要入栈的三个元素来接水
                        int mid = sta.top();
                        sta.pop();
                        if (!sta.empty()) { // 有必要
                            int h = min(height[i], height[sta.top()]) - height[mid];
                            int w = i - sta.top() - 1;
                            sum += h * w;
                        }
                    }
                    sta.push(i);
                }
            }
            return sum;
        }
    };
    ```

-   #### [LC84.柱状图中最大的矩形](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)

    ```c++
    class Solution {
    public:
        int largestRectangleArea(vector<int>& heights) {
            int sum = 0;
            for (int i = 0; i < heights.size(); i++) {
                int left = i;
                int right = i;
                for (; left >= 0; left--) { // 找到最左边的界限
                    if (heights[left] < heights[i]) break;
                }
                for (; right < heights.size(); right++) { // 找到最右边的界限
                    if (heights[right] < heights[i]) break;
                }
                int width = right - left - 1; 
                int height = heights[i];
                sum = max(sum, width * height);
            }
            return sum;
        }
    };
    ```

    ```c++
    class Solution {
    public:
        int largestRectangleArea(vector<int>& heights) {
            vector<int> maxLeft(heights.size());
            vector<int> maxRight(heights.size());
            maxLeft[0] = -1; // 避免死循环
            for (int i = 1; i < heights.size(); i++) {
                int t = i - 1;
                while (t >= 0 && heights[t] >= heights[i]) t = maxLeft[t]; // 循环往前推，maxLeft[i]代表当前位置最左边界，这样可以减少循环次数 也可以 t -= 1;但会超时
                maxLeft[i] = t;
            }
            maxRight[heights.size() - 1] = heights.size();
            for (int i = heights.size() - 2; i >= 0; i--) {
                int t = i + 1;
                while (t < heights.size() && heights[t] >= heights[i]) t = maxRight[t]; // 循环往后推，maxRight[i]代表当前位置最右边界，这样可以减少循环次数 也可以 t += 1;但会超时
                maxRight[i] = t;
            }
            int result = 0;
            for (int i = 0; i < heights.size(); i++) {
                int sum = heights[i] * (maxRight[i] - maxLeft[i] - 1); // 高 * 宽
                result = max(sum, result);
            }
            return result;
        }
    };
    ```

    ```c++
    class Solution {
    public:
        int largestRectangleArea(vector<int>& heights) {
            stack<int> sta;
            heights.insert(heights.begin(), 0); // 在头部加入元素0
            heights.push_back(0); // 在尾部加入元素0
            sta.push(0);
            int result = 0;
            for  (int i = 1; i < heights.size(); i++) { // 需要找出当前位置左右两边最近的小于当前高度的位置，需要使用单调递减栈
                if (heights[i] > heights[sta.top()]) sta.push(i); // 情况一：当前遍历的元素heights[i]小于栈顶元素heights[st.top()]的情况
                else if (heights[i] == heights[sta.top()]) { // 情况二：当前遍历的元素heights[i]等于栈顶元素heights[st.top()]的情况
                    sta.pop(); // 记录最远的位置
                    sta.push(i); 
                } else {  //情况二：当前遍历的元素heights[i]等于栈顶元素heights[st.top()]的情况
                    while (heights[i] < heights[sta.top()]) {
                        int mid = sta.top();
                        sta.pop();
                        int left = sta.top();
                        int right = i;
                        int w = right - left - 1; // 左右两边第一个小于当前高度的位置再往里就是都大于等于当前位置的高度
                        int h = heights[mid];
                        result = max(result, w * h);
                    }
                    sta.push(i);
                }
            }
            return result;
        }
    };
    ```

## 大整数操作

### 1、大整数操作的理论要点





### 2、重点题型

-  #### [Euler13.大整数加法](http://pe-cn.github.io/13/)

    ```c++
    
    ```

-  #### [大整数乘法]()

    ```c++
    
    ```

## 零碎题型

### 1、前缀和

-   #### [LC303.区域和检索](https://leetcode-cn.com/problems/range-sum-query-immutable/)

    ```c++
    
    ```

-   #### [LC304.二维区域和检索](https://leetcode-cn.com/problems/range-sum-query-2d-immutable/)

    ```c++
    
    ```

### 2、LRU缓存机制

-   #### [LC146.LRU 缓存](https://leetcode-cn.com/problems/lru-cache/)

    ```c++
    
    ```

### 3、线段树

-   #### [HZOJ223. 线段树模板](http://oj.haizeix.com/problem/223)

    ```c++
    
    ```

### 4、递归+记忆化

-   #### [Eluer14.递推](http://pe-cn.github.io/14/)

    ```c++
    
    ```

    

#### [LC946.验证栈序列](https://leetcode-cn.com/problems/validate-stack-sequences/)

#### [LC933.最近的请求次数](https://leetcode-cn.com/problems/number-of-recent-calls/)

#### [LC1021.删除最外层的括号](https://leetcode-cn.com/problems/remove-outermost-parentheses/)

#### [LC682.棒球比赛](https://leetcode-cn.com/problems/baseball-game/)

#### [LC103.二叉树的锯齿形层序遍历](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

#### [LC844.比较含退格的字符串](https://leetcode-cn.com/problems/backspace-string-compare/)



