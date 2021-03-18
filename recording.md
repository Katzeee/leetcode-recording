# 2021.3.17

##  vector

```c++
#include<vector> //头文件
```
```c++
vector<int> vec; //创建对象
```
```c++
vec.push_back(a); //尾部插入
```
```c++
vec[0]; //访问元素
```
```c++
for(it = vec.begin() ; it != vec.end() ; it++)
    cout<<*it<<endl; //迭代器访问元素
```

```c++
vec.insert(vec.begin()+i, a); //插入元素
```

```c++
vec.erase(vec.begin()+2); //删除第3个元素
```
```c++
vec.erase(vec.begin()+i,vec.end()+j); //删除区间[i,j-1];区间从0开始
```
```c++
vec.size(); //元素数量
```
```c++
vec.clear()　　　//清空
```

## stringstream

```c++
const char* charElement = stringElement.c_str(); //转string为char指针
```

```c++
int stringElement = stringElement.length(); //string长度
```

```c++
#include<sstream>
int main()
{
    stringstream ss;
    ss<<"i love eating burger";
    string s;
    while(ss>>s)
        cout<<s<<endl;
    return 0;
}

stdout:
i
love
eating
burger
```
上面4，5行也可写成
```c++
stringstream ss("i love eating burger");
```

```c++
s.find(serachWord) //若s中存在serachWord返回位置，下标从0开始，否则返回-1
```

```c++
ss.clear(); //清空储存
```

```c++
s += "a"; //添加元素，被添加的可以是char或string
```

# 2021.3.18

## stringstream

```c++
s.substr(pos, len); //取string中的子序列，pos为起始位置，len为长度，时间复杂度空间复杂度大
```

```c++
string s="1234"; 
char c[s.length() + 1]; //+1很关键，s默认以\0结尾
strcpy(c,s.c_str()); //.c_str返回的是临时数组，这样可以对c进行操作
```

## 哈希集合

```c++
unordered_set<char> occ;
```
```c++
occ.insert(2); //插入2
```
```c++
occ.erase(2); //移除
```
```c++
occ.count(2); //检测2是否在集合中，若不在返回0，原意计数，但unordered中不能有重复元素，所以只有1，0的返回值
```
```c++
occ.size(); //返回大小
```
```c++
occ.clear(); //清空集合
```
```c++
occ.empty(); //检验是否为空，若为空返回1
```
## 哈希map
```c++
unordered_map<char, int> hash; //建立一个char：int的map，前面的元素（char）不能重复
```

```c++
hash[Key] = value; //赋值
```

```c++
hash.find(Key); //查找是否存在Key，若存在返回对应迭代器。若不存在返回unordered_map::end
map.find(Key) == map.end() //用于判断Key是否存在
```

```c++
hash.count(Key); //同哈希集合
```

## vectot

```c++
vector<vector<int>> dp(n, vector<int>(n)); //二维vector定义方式，访问方式与数组相同
```

## 92.反转链表Ⅱ

>给你单链表的头指针 head 和两个整数 left 和 right ，其中 left <= right 。请你反转从位置 left 到位置 right 的链表节点，返回反转后的链表 。

- 我的解法

```c++
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int left, int right) {
        ListNode* leftNode;
        ListNode* rightNode;
        ListNode* headRe = head;
        if(left == 1)//考虑特殊情况
            leftNode = head;
        for (int i = 1 ; i <= right ; i++)
        {
            if(i == left - 1)
                leftNode = head;//下一个节点是left
            if(i == right)
                rightNode = head;
            if(head->next != NULL)
                head = head->next;
        }
        ListNode* tempLeft = new ListNode();
        if(left != 1) //保存left左边的节点信息
        {
            tempLeft->next = leftNode;
            leftNode = leftNode->next; //现在left才是对应节点
        }
        ListNode* tempRight = new ListNode();
        //if(rightNode->next != NULL)//保存right右边节点信息
        tempRight->next = rightNode->next;
        //else
        //    tempRight->next = NULL;
        ListNode* temp = new ListNode();
        ListNode* j = leftNode;
        //return j;
        for (int i = left ; i <= right ; i++)//从左节点遍历到右节点
        {
            //cout<<j->val<<endl;
            //if(j->next != NULL)
            temp->next = j->next;
            j->next = tempRight->next;
            tempRight->next = j;
            j = temp->next;
        }
        if(left != 1)
            tempLeft->next->next = tempRight->next;
        else//考虑特殊情况
            headRe = tempRight->next;
        return headRe;
    }
};
```

解法不足：将left=1的情况当作特殊情况来处理，需要分类讨论

- 穿针引线法

```c++
class Solution {
private:
    void reverseLinkedList(ListNode *head) {
        // 也可以使用递归反转一个链表，此处为原地反转
        ListNode *pre = nullptr;
        ListNode *cur = head;

        while (cur != nullptr) {
            ListNode *next = cur->next;
            cur->next = pre;
            pre = cur;
            cur = next;
        }
    }

public:
    ListNode *reverseBetween(ListNode *head, int left, int right) {
        // 因为头节点有可能发生变化，使用虚拟头节点可以避免复杂的分类讨论
        ListNode *dummyNode = new ListNode(-1);
        dummyNode->next = head;

        ListNode *pre = dummyNode;
        // 第 1 步：从虚拟头节点走 left - 1 步，来到 left 节点的前一个节点
        // 建议写在 for 循环里，语义清晰
        for (int i = 0; i < left - 1; i++) {
            pre = pre->next;
        }

        // 第 2 步：从 pre 再走 right - left + 1 步，来到 right 节点
        ListNode *rightNode = pre;
        for (int i = 0; i < right - left + 1; i++) {
            rightNode = rightNode->next;
        }

        // 第 3 步：切断出一个子链表（截取链表）
        ListNode *leftNode = pre->next;
        ListNode *curr = rightNode->next;

        // 注意：切断链接
        pre->next = nullptr;
        rightNode->next = nullptr;

        // 第 4 步：同第 206 题，反转链表的子区间
        reverseLinkedList(leftNode);

        // 第 5 步：接回到原来的链表中
        pre->next = rightNode;
        leftNode->next = curr;
        return dummyNode->next;
    }
};
```

该解法采用虚拟头结点来避免分类讨论的情况

- 一次遍历「穿针引线」反转链表（头插法）
- 整体思想是：在需要反转的区间里，每遍历到一个节点，让这个新节点来到反转部分的起始位置。下面的图展示了整个流程。
- curr：指向待反转区域的第一个节点 left；
  next：永远指向 curr 的下一个节点，循环过程中，curr 变化以后 next 会变化；
  pre：永远指向待反转区域的第一个节点 left 的前一个节点，在循环过程中不变。
![](https://pic.leetcode-cn.com/1615105296-bmiPxl-image.png)
- 先将 curr 的下一个节点记录为 next；
- 执行操作 ①：把 curr 的下一个节点指向 next 的下一个节点；
- 执行操作 ②：把 next 的下一个节点指向 pre 的下一个节点；
- 执行操作 ③：把 pre 的下一个节点指向 next。


```c++
class Solution {
public:
    ListNode *reverseBetween(ListNode *head, int left, int right) {
        // 设置 dummyNode 是这一类问题的一般做法
        ListNode *dummyNode = new ListNode(-1);
        dummyNode->next = head;
        ListNode *pre = dummyNode;
        for (int i = 0; i < left - 1; i++) {
            pre = pre->next;
        }//走到left节点前
        ListNode *cur = pre->next;
        ListNode *next;
        for (int i = 0; i < right - left; i++) {
            next = cur->next;
            cur->next = next->next;
            next->next = pre->next;
            pre->next = next;
        }
        return dummyNode->next;
    }
};
```

## 5.最长回文子串

- 给你一个字符串 `s`，找到 `s` 中最长的回文子串。
- 我的解法实际就是中心扩展解法，遍历每个元素，分别按奇偶找到最长串

```c++
class Solution {
public:
    string longestPalindrome(string s) {
        string res;
        int sLength = s.length();
        int rptr = 0, lptr = 0;
        int i = 0, count = 0, maxCount = 0;
        for(i = 0 ; i < sLength ; i++)//奇数最长串
        {
            rptr = i;
            lptr = i;//从i和i开始比对
            while(lptr >= 0 && rptr < sLength)
            {
                if(s[lptr] == s[rptr])
                {
                    lptr--;
                    rptr++;
                    count++;
                }
                else
                    break;
            }
            if(count > maxCount)
            {
                maxCount = count;
                res = s.substr(++lptr, rptr - lptr);
            }
            count = 0;
        }
        for(i = 0 ; i < sLength ; i++)//偶数最长串
        {
            rptr = i;
            lptr = i + 1;//从i和i+1开始比对
            while(lptr >= 0 && rptr < sLength)
            {
                if(s[lptr] == s[rptr])
                {
                    lptr--;
                    rptr++;
                    count++;
                }
                else
                    break;
            }
            if(count > maxCount)
            {
                maxCount = count;
                res = s.substr(++lptr, rptr - lptr);
            }
            count = 0;
        }

        return res;
    }
};
```

解法不足：分了奇偶做，遍历了两次string，可以考虑把两次while循环放入一个for中，遍历一次

- 动态规划

$$
P(i,j)=\begin{cases}
true,\,\,若子串s[i,j]是回文串\\
false,\,\,otherwise\\
\end{cases}
$$
状态转移方程：
$$
P(i,j)=P(i+1,j-1)\and(S_i==S_j)
$$

边界条件：
$$
\begin{cases}
P(i,i)=true\\
P(i,i+1)=(S_i==S_j)\\
\end{cases}
$$
则最终答案为满足$P(i,j)=true$中$j-i+1$的最大值

- **在状态转移方程中，我们是从长度较短的字符串向长度较长的字符串进行转移的，因此一定要注意动态规划的循环顺序**

```c++
class Solution {
public:
    string longestPalindrome(string s) {
        int n = s.size();
        vector<vector<int>> dp(n, vector<int>(n));
        string ans;
        for (int l = 0; l < n; ++l) {//按长度循环
            for (int i = 0; i + l < n; ++i) {
                int j = i + l;//这是l不是1
                if (l == 0) {
                    dp[i][j] = 1;
                } else if (l == 1) {
                    dp[i][j] = (s[i] == s[j]);
                } else {
                    dp[i][j] = (s[i] == s[j] && dp[i + 1][j - 1]);
                }
                if (dp[i][j] && l + 1 > ans.size()) {
                    ans = s.substr(i, l + 1);
                }
            }
        }
        return ans;
    }
};
```





