
# 📒 哈希表学习笔记（结合算法题）

## 1. 哈希表回顾

* **定义**：哈希表是一种 **键值对映射** 的数据结构，利用哈希函数将 Key 快速映射到桶（数组下标）。
* **优势**：插入、删除、查找平均复杂度 **O(1)**。
* **冲突解决**：链地址法、开放定址法。
* **典型实现**：

  * C++：`unordered_map`, `unordered_set`
  * Python：`dict`, `set`
  
![哈希表结构示意图](hash_table.png)
---

## 2. 哈希表的经典应用场景

### 2.1 查找是否存在 **配对元素**

📌 **题目：两数之和（Two Sum）**

* **思路**：遍历数组时，把 `target - nums[i]` 存在哈希表里，就能 O(1) 查到是否有对应的数。
* **核心操作**：哈希表存储「值 -> 下标」。

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int,int> hashtable;
        for (int i = 0; i < nums.size(); ++i) {
            auto it = hashtable.find(target - nums[i]);
            if (it != hashtable.end()) {
                return {it->second, i};
            }
            hashtable[nums[i]] = i;
        }
        return {};
    }
};
```

✅ **要点**：

* 利用 **哈希表 O(1) 查找**，将暴力 O(n²) 降为 O(n)。
* 哈希表常用于 **快速查找补数、差值、目标值**。

---

### 2.2 归类 / 分组问题

📌 **题目：字母异位词分组（Group Anagrams）**

* **思路**：异位词排序后结果相同，用排序后的字符串作为哈希表的 key，把同类词放到一起。
* **核心操作**：哈希表存储「排序后的字符串 -> 原始字符串列表」。

```cpp
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string,vector<string>> mp;
        for(string& str:strs){
            string key=str;  //复制一次str，复制eat比如
            sort(key.begin(),key.end());  //重新排序key，把eat变成ate
            mp[key].emplace_back(str);    //把str加到key后面，就是ate，eat
        }
        //输出
        vector<vector<string>> ans;
        for(auto it=mp.begin();it!=mp.end();++it) {
            ans.emplace_back(it->second);
        }
        return ans;
    }
};
```

✅ **要点**：

* 哈希表不仅可以存单个值，还可以存 **集合/数组**。
* 分组问题常用「**规范化后的 key**」作为哈希表的键，比如：

  * 排序字符串
  * 计数数组（字母频率）
  * 模式化表示

---

### 2.3 查找 **连续关系 / 区间扩展**

📌 **题目：最长连续序列（Longest Consecutive Sequence）**

* **思路**：用哈希表（集合）存所有数，找到连续序列的起点，再往右扩展。
* **核心操作**：哈希表存储所有数，O(1) 判断某个数是否存在。

```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_set<int> num_set;
        for(const int& num:nums){
            num_set.insert(num);  //把数组插入集合，遍历集合效率高
        }
        int le=0;
        for(const int& num:num_set){
            if(!num_set.count(num-1)){
                int curNum=num;
                int curle=1;
                while(num_set.count(curNum+1)){   //检查curNum+1在不在集合里
                    curNum+=1;
                    curle+=1;
                }
                le=max(le,curle);
            }
        }
        return le;
    }
};
```

✅ **要点**：

* 哈希表适合 **快速存在性判断**（`set.count(x)`）。
* 通过 **判断前驱不存在**，确保每个连续序列只遍历一次，避免 O(n²)。
* 平均复杂度 O(n)。

---

## 3. 三道题总结 —— 哈希表的三大典型用法

1. **快速查找映射关系**

   * 两数之和：值 → 下标
   * 常见模式：`map[target - x]`

2. **分组 / 分类存储**

   * 异位词分组：key 是排序后的字符串
   * 常见模式：`map[key].push_back(val)`

3. **快速存在性判断**

   * 最长连续序列：`set.count(x)`
   * 常见模式：用 `unordered_set` 做 membership check

---

## 4. 知识点强化

* **复杂度**：

  * 插入/查找/删除：平均 O(1)，最坏 O(n)
* **负载因子 & 扩容**：哈希表过满会影响性能
* **哈希函数设计**：应保证分布均匀，避免过多冲突

---

## 5. 总结口诀 📝

> **查找靠 `map`，分组靠 `map`，存在性靠 `set`**
> —— 面试高频，三大应用场景几乎覆盖了哈希表的主要用法。


