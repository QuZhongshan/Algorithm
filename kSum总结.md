# kSum问题总结

## 问题描述

kSum的求和问题一般是这样描述的：给你一组n个数字，然后给你一个常数target，在这一堆数字中找到k个数字，使得这k个数字的和等于所给出的target。简而言之，就是在数组中寻找k个数的和能否达到目标值。

## 注意事项

注意这一组数字可能有重复项：比如[1，1，2，3]，求3sum，target=6，在搜索的时候可能会得到两组[1，2，3]、[1，2，3]，1来自第一个1或者第二个1，但是结果其实只有一组，所以最后结果要去重。

## 求解方法

leetcode中关于kSum的主要题目有：

- [1. Two Sum](https://leetcode.com/problems/two-sum/?tab=Solutions)
- [167. Two Sum II - Input array is sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/?tab=Description)
- [15. 3Sum](https://leetcode.com/problems/3sum/?tab=Description)
- [16. 3Sum Closest](https://leetcode.com/problems/3sum-closest/?tab=Description)
- [18. 4Sum](https://leetcode.com/problems/4sum/?tab=Description)
- [454. 4Sum II](https://leetcode.com/problems/4sum-ii/?tab=Description)

首先从Two Sum开始，这道题目提供了后面k>2的题目的基本思路，也是最常考到的题目。主要有两种思路，一种是利用哈希表对元素的出现进行记录，然后进来新元素时看看能不能与已有元素配成target，如果哈希表的访问是常量操作，这种算法复杂度是O(n)。另一种思路则是先对数组进行排序，然后利用夹逼的方法找出满足条件的pair，算法的复杂度是O(nlogn)。两种方法都优于暴力解法（brute force）的O(n^2)，具体可以参见题目Two Sum的具体分析。因为这道题模型简单，但是可以考核到哈希表等基本数据结构和排序等基本算法，所以是面试中的常客。

接下来是3Sum和3Sum Closest，3Sum是使用Two Sum的第二种方法作为子操作，然后循环每个元素，寻找剩下的元素满足条件的pair即可。3Sum Closest和3Sum很类似，只是要多维护一个最小的diff，保存和target最近的值。算法的复杂度是O(n^2)，这道题使用Two Sum的第一种方法并不合适，因为当出现元素重复时用哈希表就不是很方便了。

最后是4Sum，这道题的比较优的解法是用求解一般kSum的解法进行层层二分，然后与Two Sum结合起来，基本思路是这样，实现细节还是比较复杂的。

其他相关的kSum问题都可以根据以上的问题来进行推倒得到。因为即使是4Sum都比较复杂了，所以面试中的实际难度和实现细节最多也就到这里了，kSum这种一般性的问题只需要知道思路就足以满足面试要求。

# 1. Two Sum

## 一、题意分析

### [1] LeetCode题目

[1. Two Sum](https://leetcode.com/problems/two-sum/?tab=Solutions)

### [2] 参考资料

[参考资料](http://blog.csdn.net/linhuanmars/article/details/19711387)

## 二、解法总结

### [1] 解法一：

#### 算法思路

这种算法的主要思想就是利用Map这种数据结构来简化运算，降低时间复杂度，在Java中是利用HashMap类来实现。在HashMap对象中，key值为数组中个各个数值，value值为其对应的数组下标index，开始时HashMap对象为空。当遍历数组时，首先检查HashMap对象中是否存在以target-nums[i]为key值得元素，即若当前所遍历的数组元素为一个答案，那么另一个答案是是否存在于在该元素之前的数组中，若存在，则结束循环，输出这两个结果；若不存在，则将这个数组元素和它所对应的数组下标index存入HashMap对象中，继续继续进行遍历，查看下一个数组元素。

由于只是遍历一遍数组，所以时间复杂度是O(n)，但相应的空间复杂度是O(n)

#### 代码

```
public class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] result = new int[2];
        if (nums == null || nums.length < 2)
            return result;
        
        Map<Integer, Integer> hashMap = new HashMap<Integer, Integer>();  
        for (int i = 0; i < nums.length; i++) {  
            if (!hashMap.containsKey(target - nums[i])) {  
                hashMap.put(nums[i], i);  
            } else {  
                result[0] = hashMap.get(target - nums[i]);  
                result[1] = i;  
            }
        }
        
        return result;
    }  
}  
```

#### 复杂度分析

时间复杂度：O(n)

空间复杂度：O(n)


### [2] 解法二：

#### 算法思路

这种算法的主要思想是就是对给定的数组进行排序，然后用s首尾指针的方法来找到答案。首先，需要对给定的数组进行排序，因为原数组不能改变，所以先将原数组进行复制，对复制的数组进行排序；第二步，定义两个指针low和high，分别指向排序好的数组中的第一个元素和最后一个元素，用移动首尾指针的方式来进行查找答案；第三步，找到答案之后，从原数组中查找这答案中的两个数字的下标index，最后返回结果即可。

数组进行复制的时间复杂度为O(n)，对复制后的数组进行排序需要时间O(nlogn)，二分查找的时间为O(n)，最后遍历原数组找到指定下标Index的时间为O(n)，所以总的世间复杂度为O(n+nlogn+n+n) = O(nlogn)。

#### 代码

```
public class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] result = new int[2];  
        if (nums == null || nums.length < 2)  
            return result;  
          
        int[] copyNums = new int[nums.length];  
        System.arraycopy(nums, 0, copyNums, 0, nums.length);  
        Arrays.sort(copyNums);
        
        int low = 0;  
        int high = nums.length - 1;  
        while (low < high) {  
            if (copyNums[low] + copyNums[high] < target)  
                low++;  
            else if (copyNums[low] + copyNums[high] > target)  
                high--;  
            else {  
                result[0] = copyNums[low];  
                result[1] = copyNums[high];  
                break;  
            }  
        }  
        
        int index1 = -1, index2 = -1;  
        for (int i = 0; i < nums.length; i++) {  
            if (nums[i] == result[0] && index1 == -1)  
                index1 = i;  
            else if (nums[i] == result[1] && index2 == -1)  
                index2 = i;  
        }  
        result[0] = index1;  
        result[1] = index2;  
        Arrays.sort(result);  
        
        return result;  
    }
}
```
#### 复杂度分析

时间复杂度：O(nlogn)


# 2. Two Sum II - Input array is sorted

## 一、题意分析

### [1] LeetCode题目

[167. Two Sum II - Input array is sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/?tab=Description)

### [2] 参考资料

直接看LeetCode中的参考答案即可

## 二、解法总结

#### 算法思路

这道题目是Two Sum问题的简单版本，其所给的数组是已经排好序，所以我们不需要想Two Sum问题一样再对其进行排序，直接定义首尾两个指针进行查找即可。

因为没有进行排序，所以只需遍历一遍数组即可，其时间复杂度为O(n)。

#### 代码

```
public class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int[] res = new int[2];
        if (numbers == null || numbers.length < 2)
            return res;
            
        int low = 0, high = numbers.length - 1;
        while (low < high) {
            int sum = numbers[low] + numbers[high];
            
            if (sum == target) {
                res[0] = low + 1;
                res[1] = high + 1;
                return res;
            } else if (sum < target) {
                low++;
            } else {
                high--;
            }
        }
        
        return res;
    }
}
```

#### 复杂度分析

时间复杂度：O(n)


# 3. 3Sum

## 一、题意分析

### [1] LeetCode题目

[15. 3Sum](https://leetcode.com/problems/3sum/?tab=Description)

### [2] 参考资料

[参考资料](http://blog.csdn.net/ljiabin/article/details/40620579)

同时看LeetCode中的参考答案

## 二、解法总结

#### 算法思路

这道题是Two Sum问题的扩展，brute force的时间复杂度是O(n^3)，思路为使用三层循环，对每三个数进行测试。这道题和Two Sum有所不同，使用哈希表的解法并不是很方便，因为结果数组中元素可能重复，如果不排序对于重复的处理将会比较麻烦，因此这道题一般使用排序之后夹逼的方法，总的时间复杂度为O(n^2+nlogn)=O(n^2),空间复杂度是O(n)。

#### 代码

```
public class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        ArrayList<List<Integer>> res = new ArrayList<>();
        if (nums == null || nums.length < 3)
            return res;
        
        Arrays.sort(nums);
        for (int i = 0; i + 2 < nums.length; i++) {
            if (i > 0 && nums[i] == nums[i - 1])
                continue;
            
            int j = i + 1, k = nums.length - 1;
            int target = -nums[i];
            while (j < k) {
                if (nums[j] + nums[k] == target) {
                    res.add(Arrays.asList(nums[i], nums[j], nums[k]));
                    j++;
                    k--;
                    while (j < k && nums[j] == nums[j - 1])
                        j++;
                    while (j < k && nums[k] == nums[k + 1])
                        k--;
                } else if (nums[j] + nums[k] > target) {
                    k--;
                } else {
                    j++;
                }
            }
        }
        
        return res;
    }
}
```

#### 复杂度分析

时间复杂度：O(n^2)

空间复杂度：O(n)


# 4. 3Sum Closest

## 一、题意分析

### [1] LeetCode题目

[16. 3Sum Closest](https://leetcode.com/problems/3sum-closest/?tab=Description)

### [2] 参考资料

[参考资料](http://www.cnblogs.com/springfor/p/3860175.html)

同时看LeetCode中的参考答案

## 二、解法总结

#### 算法思路

这道题跟3Sum很类似，区别就是要维护一个最小的差值，求出和目标最近的三个和。brute force时间复杂度为O(n^3)，优化的解法是使用排序之后夹逼的方法，总的时间复杂度为O(n^2+nlogn)=(n^2)。

#### 代码

```
public class Solution {
    public int threeSumClosest(int[] nums, int target) {
        if (nums == null || nums.length < 3)
            return 0;
        
        int result = nums[0] + nums[1] + nums[2];
        Arrays.sort(nums);
        
        for (int i = 0; i < nums.length - 2; i++) {
            int start = i + 1, end = nums.length - 1;
            while (start < end) {
                int sum = nums[i] + nums[start] + nums[end];
                if (sum > target) {
                    end--;
                } else {
                    start++;
                }
                
                if (Math.abs(sum - target) < Math.abs(result - target)) {
                    result = sum;
                }
            }
        }
        
        return result;
    }
}
```

#### 复杂度分析

时间复杂度：O(n^2)


# 5. 4Sum

## 一、题意分析

### [1] LeetCode题目

[18. 4Sum](https://leetcode.com/problems/4sum/?tab=Description)

### [2] 参考资料

[参考资料](http://www.programcreek.com/2013/02/leetcode-4sum-java/)

同时看LeetCode中的参考答案

## 二、解法总结

#### 算法思路

这道题要求跟3Sum差不多，只是需求扩展到四个的数字的和了。我们还是可以按照3Sum中的解法，只是在外面套一层循环，相当于求n次3Sum。我们知道3Sum的时间复杂度是O(n^2)，所以如果这样解的总时间复杂度是O(n^3)。

#### 代码

```
public class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        ArrayList<List<Integer>> res = new ArrayList<>();
        if (nums == null || nums.length < 4)
            return res;
        
        Arrays.sort(nums);
        
        for (int i = 0; i < nums.length - 3; i++) {
            if (i != 0 && nums[i] == nums[i - 1])
                continue;
            
            for (int j = i + 1; j < nums.length - 2; j++) {
                if (j > i + 1 && nums[j] == nums[j - 1])
                    continue;
                
                int low = j + 1, high = nums.length - 1;
                while (low < high) {
                    int sum = nums[i] + nums[j] + nums[low] + nums[high];
                    
                    if (sum == target) {
                        res.add(Arrays.asList(nums[i], nums[j], nums[low], nums[high]));
                        low++;
                        high--;
                        while (low < high && nums[low] == nums[low - 1])
                            low++;
                        while (low < high && nums[high] == nums[high + 1])
                            high--;
                    } else if (sum < target) {
                        low++;
                    } else {
                        high--;
                    }
                }
            }
        }
        
        return res;
    }
}
```

#### 复杂度分析

时间复杂度：O(n^3)

# 6. 4Sum II

## 一、题意分析

### [1] LeetCode题目

[454. 4Sum II](https://leetcode.com/problems/4sum-ii/?tab=Description)

### [2] 参考资料

直接看LeetCode中的参考答案即可

## 二、算法总结

#### 算法思路

这道题目是很巧妙，它所给出的是四个不同的数组，而并不是只有一个数组，那么在求解的时候可以将这个问题分解为两个Two Sum的问题。首先，将前两个数组的所有可能的和反导一个map结构中；之后，对于后两个数组中每个可能的和，求出他的相反数，再直接在数组中查找是否有这个相反数即可，若存在，则满足条件，反之就是不满足。

因为分解为了两个Two Sum的问题，所以时间复杂度是O(n^2)。

#### 代码

```
public class Solution {
    public int fourSumCount(int[] A, int[] B, int[] C, int[] D) {
        int len = A.length;
        int res = 0;
        
        HashMap<Integer, Integer> map = new HashMap<Integer, Integer>();
        for (int i = 0; i < len; i++) {
            for (int j = 0; j < len; j++) {
                int sum = A[i] + B[j];
                map.put(sum, map.getOrDefault(sum, 0) + 1);
            }
        }
        
        for (int i = 0; i < len; i++) {
            for (int j = 0; j < len; j++) {
                int sum = -(C[i] + D[j]);
                
                if (map.containsKey(sum)) {
                    res += map.get(sum);
                }
            }
        }
        
        return res;
    }
}
```

#### 复杂度分析

时间复杂度：O(n^2)



