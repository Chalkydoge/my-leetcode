# 01字典树

> 处理位运算的高效结构

## 介绍

- 计算机内部的所有数据存储为`01`串;

- 例如int(32-bit), 可以表示为一个32位长的只含有0,1两种字符的一种字符串;

- 01-Trie就是利用了数据存储的特点,通过树的结构对于01序列进行高效存储;

## 实现

```cpp
    // 01字典树 节点信息
    struct TreeNode{
        int count = 0; // 以该节点为根的子数中节点个数
        TreeNode* child[2]{}; // 该节点的孩子节点指针
    };

    // 将一个数据化为01序列,插入到字典树中
    void insert(TreeNode* root, int num){
        for(int i = 31; i >= 0; --i){
            int index = (num >> i) & 0x1; 
            if(!root->child[index]){
                root->child[index] = new TreeNode();
            }
            root = root->child[index];
            root->count++;
        }
    }
```

## 使用

### 1803. 统计异或值在范围内的数对有多少

给你一个整数数组 nums （下标 从 0 开始 计数）以及两个整数：low 和 high ，请返回 漂亮数对 的数目。

漂亮数对 是一个形如 (i, j) 的数对，其中 0 <= i < j < nums.length 且 low <= (nums[i] XOR nums[j]) <= high 。

- **提示**
  - `1 <= nums.length <= 2 * 10^4`
  - `1 <= nums[i] <= 2 * 10^4`
  - `1 <= low <= high <= 2 * 10^4`

- **解析**

数据范围超过10000就意味着如果我们试图暴力的寻找每一个二元组,再对其分别验证是否符合要求是一定会超时的
所以我们需要一种更加高效的方法,可以获得当前元素加入后能够形成的数对组数的信息;

这里发现两个数字的异或是一种对01序列的操作, 而高效的保存位信息就可以使用01树

对于当前的nums[i]我们要求的是满足`low <= nums[k] ^ X <= high, k < i` 的所有个数
因此对于每一个元素,先计算树中之前所有符合要求的元素个数,然后我们再依次将数组元素加入字典树,最后相加即可;

如何获得相应的个数?
对于当前的数字`num`

- DFS遍历: `num = {0,1}*, low = 01110` 要求出所有`num ^ x <= low`的数组中x的个数
  
  - 当前的`low`位为1: 此时,无论`num`对应的位是0/1, 以这个节点为根的数字都符合要求(因为异或之后对应的为数值最多为1)
  - 当前的`low`位位0: 此时,为了使得异或之后的值为0, 只能选择与当前数位值相同的子树, 继续遍历这一棵子树

- **代码**

```cpp
class Solution {
public:
    // 异或值 在[0, high] - [0, low - 1] 的个数

    // 01字典树 节点信息
    struct TreeNode{
        int count = 0; // 以该节点为根的子数中节点个数
        TreeNode* child[2]{}; // 该节点的孩子节点指针
    };

    void insert(TreeNode* root, int num){
        for(int i = 31; i >= 0; --i){
            int index = (num >> i) & 0x1; 
            if(!root->child[index]){
                root->child[index] = new TreeNode();
            }
            root = root->child[index];
            root->count++;
        }
    }

    // 找出满足 num ^ (x) <= low 的节点个数
    int smaller(int num, int low, TreeNode* root){
        int count = 0;
        for(int i = 31; i >= 0; --i){
            if(!root){
                return count;
            }
            int num_index = (num >> i) & 0x1;
            int low_index = (low >> i) & 0x1;
            if(low_index){
                if(root->child[num_index]){
                    count += root->child[num_index]->count; // 该子树的所有元素都满足条件(不需要继续向下遍历)
                }
                root = root->child[1 - num_index]; // 观察另一侧是否存在可能的元素
            }
            else{
                root = root->child[num_index]; // 只能异或值为0, 即选择与当前数位一致的子树
            }
        }
        return count;
    }

    int countPairs(vector<int>& nums, int low, int high) {
        TreeNode* root = new TreeNode();
        int ans = 0;
        for(int num: nums){
            ans += (smaller(num, high + 1, root) - smaller(num, low, root));
            insert(root, num);
        }
        return ans;
    }
};
```
