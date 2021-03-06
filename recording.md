

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

# 2021.3.19

## string

- 遍历string的三种方法
- 方法一

```c++
for (size_t i = 0; i < str.size(); i++)
```
  - 方法二

```c++
string::iterator it = str.begin();//返回第一个位置的迭代器（类似于指针）
while (it != str.end())//str.end()是最后一个数据的下一个位置
```
顺序表类似

```c++
vector<int>::iterator vit = v.begin();
while (vit != v.end())
```
  - 方法三

```c++
for (auto ch : str)//ch依次取的是str里面的字符,直到取完为止
```

## dp

- 从对角线往右上遍历的两种方法
- 一直斜向遍历

| 1    | 4    | 6    |
| ---- | ---- | ---- |
|      | 2    | 5    |
|      |      | 3    |

```c++
for (l = 0 ; l < len ; l++)
    for(i = 0 ;i < len - l ; i++)
    {
        j = i + l;
        dp[i][j] = 
    }
```
```c++
 for (int i = 0; i < n; i++) 
        for (int j = 0; j < n - i; j++) 
            dp[j][i + j] = //这也是对角线遍历
```
- 从对角线左上出发每列向上遍历

<img src="https://pic.leetcode-cn.com/01d7823c519c17b7a9880544f438d7692a882077419d217bddc6d38f0cf0861e-image.png" style="zoom:40%">


```c++
for (j = 1 ; j < len ; j++)
    for(i = j - 1 ; i >= 0 ; i--)
		dp[i][j] = 
```

- 从对角线右下出发每行向右遍历

<img src="https://pic.leetcode-cn.com/f0328321e0a62510853c9605339e4a3e5107d5f48752e8dea15ab929f4223d34-image.png" style="zoom:40%">

```c++
for (i = len - 2 ; i >= 0)
    for (j = i ; j < len ; j++)
        dp[i][j] = 
```

## 877.石子游戏

> 亚历克斯和李用几堆石子在做游戏。偶数堆石子排成一行，每堆都有正整数颗石子 piles[i] 。
>
> 游戏以谁手中的石子最多来决出胜负。石子的总数是奇数，所以没有平局。
>
> 亚历克斯和李轮流进行，亚历克斯先开始。 每回合，玩家从行的开始或结束处取走整堆石头。 这种情况一直持续到没有更多的石子堆为止，此时手中石子最多的玩家获胜。
>
> 假设亚历克斯和李都发挥出最佳水平，当亚历克斯赢得比赛时返回 true ，当李赢得比赛时返回 false 。

- 动态规划

状态转移方程
$$
dp[i][j] = \max(piles[i] - dp[i+1][j],piles[j]-dp[i][j-1])
$$
其中$\textit{dp}[i][j]$ 表示**当**剩下的石子堆为下标 $i$ 到下标 $j$ 时，当前玩家与另一个玩家的石子数量之差的最大值(只考虑这个回合及之后回合)，注意当前玩家不一定是先手 $\text{Alex}$

那下以手$dp[i+1][j]$或$dp[i][j-1]$就是对方赢我的，所以本次的分数就是我拿$piles[i]$减$dp[i+1][j]$或我拿$piles[j]$减$dp[i][j-1]$

边界条件
$$
dp[i][i]=piles[i]
$$
当只有一堆石头的时候，则分差为剩的石子个数

```c++
class Solution 
{
    public:
        bool stoneGame(vector<int>& piles) 
        {
            int len = piles.size();
            vector<vector<int>> dp(len, vector<int>(len));
            for (int l = 0 ; l < len ; l++)
                for(int i = 0 ; i < len - l ; i++)
                {
                    int j = i + l;
                    if(i == j)
                        dp[i][j] = 0;
                    else
                        dp[i][j] = max(piles[i] - dp[i + 1][j], piles[j] - dp[i][j - 1]);
                }
            return dp[0][len - 1] > 0;//返回石子为最初情况下时是否获胜的情况，即是否大于0
        }
};
```

- 三维动态规划

<img src="https://pic.leetcode-cn.com/1608206974-lyiDJs-image-20201215204408201.png" style="zoom:100%">

下面解释上图，$f[i][j]$表示下标索引在[i...j]范围内，即piles[i]到piles[j]这个范围内，先手为了利益最大化，拿到的最多的石子的数量，以及后手为了利益最大化，拿到的最多的石子的数量(x,y)，其中x表示先手拿到的，y表示后手拿到的

- 情况一
  - 当在区间[0...0],[1...1],[2...2],[3...3] 这个范围内，因为只有一个石子，先手拿走后，不剩石子了，所以$f[i][i]$ 分别为(5,0),(3,0),(4,0),(5,0)

- 情况二
  - 当在区间[0...1]范围内：先手如果拿走左边的[0]=5留给后手的是[1]=3,当然，也可以从右边开始拿，但是没从左边拿好，即$f[0][1]$=(5,3)
  - 当在区间[1...2]范围内：先手如果拿走左边的[1]=3留给后手的是[2]=4,当然，也可以从右边开始拿，比从左边拿好，即$f[1][2]$=(4,3)
  - 当在区间[2...3]范围内：先手如果拿走左边的[2]=4留给后手的是[3]=5,当然，也可以从右边开始拿，比从左边拿好，即$f[2][3]$=(5,4)

- 情况三
  - 当在区间[0...2]范围内：先手如果拿走左边的[0]=5,留给后手的是下标索引[1...2]这个范围，这时候按图上去找的话，得到$f[1][2]$=(4,3)
  - 这时候，当到了这一轮的时候，**此前的先手变成了这一轮的后手**，只能去拿(4,3)中的3,也就是说先手能拿到5+3=8,后手只能拿到4，即$f[0][2]$=(8,4)
  - 同样的道理可以得到$f[1][3]$=(8,4)

- 情况四

  - 当在区间[0...3]范围内：先手如果拿走左边的[0]=5,留给后手的是下标索引[1...3]这个范围，这时候按图上去找的话，得到$f[1][3]$=(8,4)

这时候，当到了这一轮的时候，此前的先手变成了这一轮的后手，只能去拿(8,3)中的4,也就是说先手能拿到5+4=9,后手只能拿到8，即$f[0][3]$=(9,8)，这时候相当于从头到尾的区间都拿完了，先手拿了9，后手拿了8，先手拿的比后手多

```java
public boolean stoneGame(int[] piles) {
        int n = piles.length;
        //f[i][j][0] 表示 从[i...j]这个区间，先手能拿到的最多的石子，f[i][j][1]是后者能拿到的最多的石子
        int[][][] f = new int[n][n][2];
        //初始化对角线
        for (int i = 0; i < n; i++) f[i][i][0] = piles[i];
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < n - i; j++) {
                //开始斜向遍历
                int x = j, y = i + j;
                int left = piles[x] + f[x + 1][y][1];
                int right = piles[y] + f[x][y - 1][1];
                if (left >= right) {
                    f[x][y][0] = left;
                    f[x][y][1] = f[x + 1][y][0];
                } else {
                    f[x][y][0] = right;
                    f[x][y][1] = f[x][y - 1][0];
                }
            }
        }
        return f[n - 1][n - 1][0] > f[n - 1][n - 1][1];
    }
```

## 位图

>今天我们所介绍的数据结构叫做位图，在谈什么是位图之前我们先来看一道"非常简单的题"：有40亿个无符号的整型数据，现在给定一个目标数字，判断这个数字是否在这40亿数据中。题目看起来确实非常简单，有的同学说直接遍历一遍不就ok了吗？还有的同学给出了更高效的查找方式就是将这些数字排序然后进行二分查找。但是，这是有问题的，问题并不在于你搜索这个数字的效率问题，而是你在遍历也好排序也罢，这些数字在内存中放的下么？
>
>一个整型int就是4个字节，10亿个int差不多已经需要4G的内存了，40亿个int就是16G。所以这里方法行不通的根本原因实际上是内存不够，但是我们今天的讲的位图却能很好的帮助我们处理这个问题。

位图即用更小的储存单元来处理这些数据，如1bit代表一个数

- 设计位图

```c++
class BitMap
{
public:
	BitMap(size_t range)
	{
		//右移5位相当于除以32，加1是因为小于32的数字如果与32相除则得到0
		_bitTable.resize((range >> 5) + 1);//range表示需要存入的数字的范围，即最大值，如存32个就开辟2个int的位置（1int=4bytes=32bits）
	}
	
private:
	vector<int> _bitTable;
};
```

- 存入元素

```c++
	void SetBit(size_t x)
	{
		size_t index = x >> 5;//求出存在第几个int里
		size_t num = x % 32;//求出存在int的第几位里

		_bitTable[index] |= (1 << num);
	}
```

- 删除元素

**~(1 << num)**表示出了num位为0，其余位都为1

```c++
	void RemoveBit(size_t x)
	{
		size_t index = x >> 5;
		size_t num = x % 32;

		_bitTable[index] &= ~(1 << num);
	}
```

- 查找元素

```c++
	bool TestBit(size_t x)
	{
		size_t index = x >> 5;
		size_t num = x % 32;

		return _bitTable[index] & (1 << num);
	}
```

## KMP算法匹配子列

关键点在于next数组，而next数组可以直接通过模式串得到，当到指针j发生不匹配时，取前面所有的串$[1,j-1]$比较前后缀，取前后缀相等时的元素数+1即为next[j]的值

# 2021.3.21

## 73.矩阵置零

> 给定一个 `*m* x *n*` 的矩阵，如果一个元素为 **0** ，则将其所在行和列的所有元素都设为 **0** 。请使用 **[原地]** 算法。

- 我的做法，常规的O(m+n)的空间复杂度，用数组记录哪行哪列被置零

```c++
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        int mSize = matrix.size();
        int nSize = matrix[0].size();
        vector<int> flagm(mSize);
        vector<int> flagn(nSize);
        for (int i = 0 ; i < mSize ; i++)
            for(int j = 0 ; j < nSize ; j++)
                if(matrix[i][j] == 0)
                {
                    flagm[i] = 1;
                    flagn[j] = 1;
                }
        for (int i = 0 ; i < mSize ; i++)
            if (flagm[i] == 1)
                for (int j = 0 ;j < nSize ; j++)
                    matrix[i][j] = 0;

        for (int j = 0 ; j < nSize ; j++)
            if(flagn[j] == 1)
                for (int i = 0 ; i < mSize ; i++)
                    matrix[i][j] = 0;
    }
};
```

算法不足：没有达到常数复杂度，因为在想算法如何改进，如何记录被处理的数，没有想到把数统一记录，想不到新的改进方法

- 常数空间复杂度

我们可以用矩阵的第一行和第一列代替方法一中的两个标记数组，以达到 O(1)的额外空间。但这样会导致原数组的第一行和第一列被修改，无法记录它们是否原本包含 0。因此我们需要额外使用两个标记变量分别记录第一行和第一列是否原本包含 0。

```c++
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        int m = matrix.size();
        int n = matrix[0].size();
        int flag_col0 = false, flag_row0 = false;
        for (int i = 0; i < m; i++) {
            if (!matrix[i][0]) {
                flag_col0 = true;
            }
        }
        for (int j = 0; j < n; j++) {
            if (!matrix[0][j]) {
                flag_row0 = true;
            }
        }//扫第一行第一列判断是否需要置零
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (!matrix[i][j]) {
                    matrix[i][0] = matrix[0][j] = 0;
                }
            }
        }
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (!matrix[i][0] || !matrix[0][j]) {
                    matrix[i][j] = 0;
                }
            }
        }//扫整个数组记录在第一行第一列
        if (flag_col0) {
            for (int i = 0; i < m; i++) {
                matrix[i][0] = 0;
            }
        }
        if (flag_row0) {
            for (int j = 0; j < n; j++) {
                matrix[0][j] = 0;
            }
        }
    }
};
```

考虑常数级空间复杂度时，不一定是算法上的改进，可能是考虑利用原有的储存空间去储存原需要另外空间的东西

# 2021.3.22

## 191.位1的个数

>编写一个函数，输入是一个无符号整数（以二进制串的形式），返回其二进制表达式中数字位数为 '1' 的个数（也被称为**汉明重量**）。

- 我的解法：常规遍历

```c++
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int count = 0;
        for (int i = 0 ; i < 32 ; i++)
        {
            if(n & 0x1 == 1)
                count++;
            n = n >> 1;
        }
        return count;
    }
};
```

一开始想到用分治做，后来发现分治还是得提取每几位数字，可能还需要一次遍历存入数组，那不如直接遍历判断

- 位运算

观察这个运算：n&(n - 1)，其预算结果恰为把 n 的二进制位中的最低位的 1 变为 0 之后的结果。
也就是说只要一直做n&(n - 1)直到数字变为0，则进循环的次数便是1的个数

```c++
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int ret = 0;
        while (n) {
            n &= n - 1;
            ret++;
        }
        return ret;
    }
};
```

- 分治：从每两位到每四位一直到全部分别统计1的个数

```c++
class Solution {
public:
    int hammingWeight(uint32_t n) {
        n = (n & (0x55555555)) + ((n >> 1) & (0x55555555));
        n = (n & (0x33333333)) + ((n >> 2) & (0x33333333));
        n = (n & (0x0F0F0F0F)) + ((n >> 4) & (0x0F0F0F0F));
        n = (n & (0x00FF00FF)) + ((n >> 8) & (0x00FF00FF));
        n = (n & (0x0000FFFF)) + ((n >> 16) & (0x0000FFFF));
        return n;
    }
};
```

- 递归：类似循环，但每次判断一位，效率不高

```c++
class Solution {
public:
    int hammingWeight(uint32_t n) {
        if (n < 2)
            return n;
        return hammingWeight(n / 2) + n % 2;
    }
};
```

# 2021.3.23

## 迭代器

- 正向迭代器

```c++
容器类名::iterator 迭代器名;
```
- 常量正向迭代器

```c++
容器类名::const_iterator 迭代器名;
```
- 反向迭代器

```c++
容器类名::reverse_iterator 迭代器名;
```
- 常量反向迭代器

```c++
容器类名::const_reverse_iterator 迭代器名;
```

- 使用方法
  - 通过迭代器可以读取它指向的元素，`*迭代器名`就表示迭代器指向的元素。通过**非常量迭代器**还能修改其指向的元素。
  - 迭代器都可以进行`++`操作。反向迭代器和正向迭代器的区别在于：
    - 对正向迭代器进行`++`操作时，迭代器会指向容器中的后一个元素；
    - 而对反向迭代器进行`++`操作时，迭代器会指向容器中的前一个元素。

- 迭代器的功能分类

  - **不同容器**的迭代器，其功能强弱有所不同。容器的迭代器的功能强弱，决定了该容器是否支持 STL 中的某种算法。例如，排序算法需要通过随机访问迭代器来访问容器中的元素，因此有的容器就不支持排序算法。

    常用的迭代器按功能强弱分为输入、输出、**正向**、**双向**、**随机访问**五种，这里只介绍常用的三种。

    1) 正向迭代器。假设 p 是一个正向迭代器，则 p 支持以下操作：++p，p++，*p。此外，两个正向迭代器可以互相赋值，还可以用`==`和`!=`运算符进行比较。

    2) 双向迭代器。双向迭代器具有正向迭代器的全部功能。除此之外，若 p 是一个双向迭代器，则`--p`和`p--`都是有定义的。`--p`使得 p 朝和`++p`相反的方向移动。

    3) 随机访问迭代器。随机访问迭代器具有双向迭代器的全部功能。若 p 是一个随机访问迭代器，i 是一个整型变量或常量，则 p 还支持以下操作：

    - p+=i：使得 p 往后移动 i 个元素。
    - p-=i：使得 p 往前移动 i 个元素。
    - p+i：返回 p 后面第 i 个元素的迭代器。
    - p-i：返回 p 前面第 i 个元素的迭代器。
    - p[i]：返回 p 后面第 i 个元素的引用。
  
- 此外，两个随机访问迭代器 p1、p2 还可以用 <、>、<=、>= 运算符进行比较。`p1<p2`的含义是：p1 经过若干次（至少一次）`++`操作后，就会等于 p2。其他比较方式的含义与此类似。
  
- 对于两个随机访问迭代器 p1、p2，表达式`p2-p1`也是有定义的，其返回值是 p2 所指向元素和 p1 所指向元素的序号之差（也可以说是 p2 和 p1 之间的元素个数减一）。

下表所示为不同容器的迭代器的功能。

| 容器           | 迭代器功能   |
| -------------- | ------------ |
| vector         | 随机访问     |
| deque          | 随机访问     |
| list           | 双向         |
| set / multiset | 双向         |
| map / multimap | 双向         |
| stack          | 不支持迭代器 |
| queue          | 不支持迭代器 |
| priority_queue | 不支持迭代器 |

## auto关键字

C++11 auto可以在声明变量的时候根据变量初始值的类型自动为此变量选择匹配的类型

1.auto声明的变量必须要初始化，否则编译器不能判断变量的类型。

2.auto不能被声明为返回值，auto不能作为形参，auto不能被修饰为模板参数

- 在遍历容器时，如果用冒号遍历相当于是拷贝了一份数据进行遍历，无法更改原容器内数据，而如果用迭代器时是会改变容器内数据的

```c++
int main()
{
    std::map<int,int> Data;
    Data.insert(make_pair(1,1));
    for (auto iter:DATA)
        iter.second = 5;//这里不会修改map的值
    for (auto iter = Data.begin() ; iter != Data.end() ; ++iter)
        iter->second = 5;//这里map的值会变成5
    for (auto &iter:Data)
        iter.second = 6;//传引用的方法也可以修改map的值
    return 0;
}
```

## 341.扁平化嵌套列表迭代器

>给你一个嵌套的整型列表。请你设计一个迭代器，使其能够遍历这个整型列表中的所有整数。
>
>列表中的每一项或者为一个整数，或者是另一个列表。其中列表的元素也可能是整数或是其他列表。
>

这题我没看懂题，看完解答之后大概懂了，以下为题意解释

> 本题定义了一个类 NestedInteger ，这个类可以存储 int  或 vector<NestedInteger> ；所以称它是一个「嵌套列表」。因为他是vector容器定义的，所以本身是有vector容器的方法的，如可以通过迭代器访问其元素。它有三个方法：
>
> - isInteger() ，判断当前存储的对象是否为 int；
>
> - getInteger() , 如果当前存储的元素是 int 型的，那么返回当前的结果 int，否则调用会失败；
>
> - getList() ，如果当前存储的元素是 List<NestedInteger> 型的，那么返回该 List，否则调用会失败。
>
> 所有可以通过其isInteger方法判断本次迭代到的是否是int，如果不是可以通过getList获得一个新的属性为vector<NestedInteger>的变量，因此可以通过递归来铺平。
>
>而「扁平化嵌套列表迭代器」说的是，我们需要设计一个迭代器，这个迭代器是把「嵌套列表」铺平（拆包）成各个 int，然后每次调用 hasNext() 来判断是否有下一个整数，通过 next() 返回下一个整数。

- 深度优先遍历DFS

```c++
/**
 * // This is the interface that allows for creating nested lists.
 * // You should not implement it, or speculate about its implementation
 * class NestedInteger {
 *   public:
 *     // Return true if this NestedInteger holds a single integer, rather than a nested list.
 *     bool isInteger() const;
 *
 *     // Return the single integer that this NestedInteger holds, if it holds a single integer
 *     // The result is undefined if this NestedInteger holds a nested list
 *     int getInteger() const;
 *
 *     // Return the nested list that this NestedInteger holds, if it holds a nested list
 *     // The result is undefined if this NestedInteger holds a single integer
 *     const vector<NestedInteger> &getList() const;
 * };
 */


class NestedIterator {
private:
    vector<int> vals;//将元素存入该vector
    vector<int>::iterator cur;

    void dfs(const vector<NestedInteger> &nestedList) {
        for (auto &nest : nestedList) {
            if (nest.isInteger()) {
                vals.push_back(nest.getInteger());
            } else {
                dfs(nest.getList());//若本次元素不是int，则递归
            }
        }
    }

public:
    NestedIterator(vector<NestedInteger> &nestedList) {
        dfs(nestedList);
        cur = vals.begin();//将cur作为迭代器访问vals容器
    }

    int next() {
        return *cur++;
    }

    bool hasNext() {
        return cur != vals.end();
    }
};


/**
 * Your NestedIterator object will be instantiated and called as such:
 * NestedIterator i(nestedList);
 * while (i.hasNext()) cout << i.next();
 */
```

# 2021.3.24

## vector

vector.end()指向vector最后一位的**后一位**！！！

vector切片

```c++
vector<int> v(n.begin(), n.begin() + 1);
```

vector找最大值

```c++
vector<int>::iterator pos = max_element(v.begin(), v.end());
```

## multiset

 c++语言中，multiset是<set>库中一个非常有用的类型，它可以看成一个序列，插入一个数，删除一个数都能够在$O(\log n)$的时间内完成，而且他能时刻保证序列中的数是有序的，而且序列中可以存在重复的数。

-  常用method

| **操作**     | **效果**                 |
| ------------ | ------------------------ |
| c.size()     | 返回当前的元素数量       |
| c.max_size() | 返回能容纳的元素最大数量 |
| count (elem) | 返回元素值为elem的个数                          |
| find(elem)   | 返回元素值为elem的第一个元素，如果没有返回end() |
| lower _bound(elem) | 返回元素值为elem的第一个可安插位置，也就是元素值 >= elem的第一个元素位置 |
| upper _bound (elem) | 返回元素值为elem的最后一个可安插位置，也就是元素值 > elem 的第一个元素位置 |
| equal_range (elem) | 返回elem可安插的第一个位置和最后一个位置，也就是元素值==elem的区间 |

##456.132模式

> 给你一个整数数组 nums ，数组中共有 n 个整数。132 模式的子序列 由三个整数 nums[i]、nums[j] 和 nums[k] 组成，并同时满足：i < j < k 和 nums[i] < nums[k] < nums[j] 。
>
> 如果 nums 中存在 132 模式的子序列 ，返回 true ；否则，返回 false 。
>

- 我的解法：类似枚举j，但考虑j一定是最大的，从最大的往次大的一个个找

```c++
class Solution {
public:
    int findMax(vector<int>& nums, vector<int>::iterator &pos)
    {
        pos = max_element(nums.begin(), nums.end());
        return *pos;
    }
    int findMin(vector<int>& nums, vector<int>::iterator &pos)
    {
        pos = min_element(nums.begin(), nums.end());
        return *pos;
    }
    bool find132pattern(vector<int>& nums) {
        vector<int>::iterator maxPos;
        vector<int>::iterator minPos;
        vector<int>::iterator Pos;
        int Max, leftMin, rightMax;
        while(nums.size() >= 3)
        {
            Max = findMax(nums, Pos);
            if(Pos == nums.begin() || Pos + 1 == nums.end())
            {
                nums.erase(Pos);
                continue;
            }
            vector<int> left(nums.begin(), Pos);
            vector<int> right(Pos + 1, nums.end());
            leftMin = findMin(left, minPos);
            rightMax = findMax(right, maxPos);
            if(rightMax == Max)
            {
                right.erase(maxPos);
                rightMax = findMax(right, maxPos);
            }
            if(leftMin < rightMax && rightMax < Max)
                return 1;
            else
                nums.erase(Pos);
        }
        return 0;
    }
};
```

解法不足：没有维护之前获得的信息，每次更换MAX时都重新搜索了需要的信息，如左边的最小值，该方法只是一个$O(n^2)$的算法

- 枚举3的同时维护1

```c++
class Solution {
public:
    bool find132pattern(vector<int>& nums) {
        int n = nums.size();
        if (n < 3) {
            return false;
        }

        // 左侧最小值
        int left_min = nums[0];
        // 右侧所有元素
        multiset<int> right_all;

        for (int k = 2; k < n; ++k) {
            right_all.insert(nums[k]);
        }//O(nlog n)

        for (int j = 1; j < n - 1; ++j) {
            if (left_min < nums[j]) {//枚举3
                auto it = right_all.upper_bound(left_min);
                if (it != right_all.end() && *it < nums[j]) {
                    return true;
                }
            }
            left_min = min(left_min, nums[j]);//维护最小值
            right_all.erase(right_all.find(nums[j + 1]));
        }

        return false;
    }
};
```

该算法采用multiset来进行查找操作，时间复杂度较低，同时维护了左侧最小值$O(n\log n)$

- 枚举1

使用单调栈来维护

如果我们从左到右枚举 1 的下标 i，那么 j, k 的下标范围都是减少的，这样就不利于对它们进行维护。因此我们可以考虑从右到左枚举 i。

那么我们应该如何维护 j,k 呢？在 132 模式中，如果 1<2 并且 2<3，那么根据传递性，1<3 也是成立的，那么我们可以使用下面的方法进行维护：

- 我们使用一种数据结构维护所有遍历过的元素，它们作为 2 的候选元素。
- 每当我们遍历到一个新的元素时，就将其加入数据结构中；

- 在遍历到一个新的元素的同时，我们可以考虑其是否可以作为 3。如果它作为 3，**那么数据结构中所有严格小于它的元素都可以作为 2，我们将这些元素全部从数据结构中移除**，并且使用一个变量维护所有被移除的元素的最大值。这些被移除的元素都是可以真正作为 2 的，并且元素的值越大，那么我们之后找到 1 的机会也就越大。

那么这个「数据结构」是什么样的数据结构呢？我们尝试提取出它进行的操作：

- 它需要支持添加一个元素；

- 它需要支持移除所有严格小于给定阈值的所有元素；

- 上面两步操作是「依次进行」的，即我们先用给定的阈值移除元素，再将该阈值加入数据结构中。

```c++
class Solution {
public:
    bool find132pattern(vector<int>& nums) {
        stack<int> st;//单调递减栈，因为我们要找nums[j]>nums[k]，也就是第一个比nums[k]大的数，找到了就说明满足了不等式132的后半部分3>2
        //也就是我们要找到第一个比2大的3，同时记录这个2有多大和左边的数比较
        int n = nums.size(), k = INT_MIN;
        for(int i = n - 1; i >= 0; i--){
            if(nums[i] < k) return true;//此时j在栈顶，如果有num[i]<k，那么栈顶元素一定比k大
            while(!st.empty() and st.top() < nums[i]) { //移除元素
                k = max(k,st.top()); st.pop();
            }
            st.push(nums[i]);//如果不能作为i则压入栈
        }
        return false;
    }
};
```

## 739.每日温度

>请根据每日 气温 列表，重新生成一个列表。对应位置的输出为：要想观测到更高的气温，至少需要等待的天数。如果气温在这之后都不会升高，请在该位置用 0 来代替。
>
>例如，给定一个列表 temperatures = [73, 74, 75, 71, 69, 72, 76, 73]，你的输出应该是 [1, 1, 4, 2, 1, 1, 0, 0].
>

同样是一个单调栈的问题，但我在做的时候没有想到可以压入下标，所以在填result数组的时候卡在了如何计算离当前循环的位置差多少，我想到用map记录，但不知道c++如何实现，但看过答案后思考发现我压入的**每日温度**实际上可以通过**下标访问**到，所以只需要压入下标就可以了

- 单调栈

```c++
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& T) {
        int n = T.size();
        vector<int> ans(n);
        stack<int> s;
        for (int i = 0; i < n; ++i) {
            while (!s.empty() && T[i] > T[s.top()]) {
                ans[s.top()] = i - s.top();
                s.pop();
            }
            s.push(i);//压入下标
        }
        return ans;
    }
};
```
- 贪心

计算ans[i]时，看下T[i+1]，如果比T[i]大，直接得到答案1；否则，比T[i]大的下一个数的位置(pos = i+1)，至少都是pos+ans[i+1]表达的位置。

相当于对自己遍历过一次的地方有记录

如已知ans[3]为2的时候，在找ans[2]的时候会直接跳两个

```c++
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& T) {
        int n = T.size();
        vector<int> ans(n);
        int pos;
        for(int i = n - 2 ; i >=0 ; i--)//从右往左填入ans[i]，为了在找ans[i-1]时可以由ans[i]提供一些信息
        {
            pos = i + 1;
            while(T[pos] <= T[i])
            {
                if(ans[pos] > 0) 
                {
                    pos += ans[pos];
                } 
                else 
                {
                    pos = i;//如果ans[pos]等于0，就说明他(T[pos])右边没有比他(T[pos])大的了
                    //并且如果能进这个while循环，说明当前我查看的T[pos]比我当前想找的这个T[i]要小
                    //那此时说明右边再也没有比我T[i]大的了，那我就让pos=i，后面让ans[i]=pos-i=0
                    break;
                }
            }
            ans[i] = pos - i;
        }
        return ans;
    }
};
```

## 单调栈

单调栈就是从数组中找到左右两边比你大的数或者比你小的数而且时间复杂度为$$O(N)$$。

可以以 O(1) 的时间复杂度得知某个位置**左右两侧比他大（或小）的数的位置**，当你需要高效率获取某个位置左右两侧比他大（或小）的数的位置的的时候就可以用到单调栈。

求解数组中元素右边第一个比它**小**的元素的下标，从前往后，构造单调递**增**栈；
求解数组中元素右边第一个比它**大**的元素的下标，从前往后，构造单调递**减**栈；
求解数组中元素左边第一个比它**大**的元素的下标，从后往前，构造单调递**减**栈；
求解数组中元素左边第一个比它**小**的元素的下标，从后往前，构造单调递**增**栈。

# 2021.3.27

## priority_queue

C++中优先队列是默认一个大根堆

```c++
//升序队列
priority_queue <int,vector<int>,greater<int> > q;//greater后面一定要有空格，不然是右移运算符>>
//降序队列
priority_queue <int,vector<int>,less<int> >q;
```

```c++
empty() 　　 //如果队列为空，则返回真
pop()　　　　//删除队顶元素，删除第一个元素
push() 　　 //加入一个元素
size() 　　　 //返回优先队列中拥有的元素个数
top() 　　　　//返回优先队列对顶元素，返回优先队列中有最高优先级的元素
```

## 快速排序

```c++
#include<iostream>
using namespace std;
void quickSort(int a[], int m,int n);
int partion(int a[], int m, int n);
int main()
{
	int a[] = { 6,1,2,7,9,3,4,5,10,8 };
	int m = 0;
	int n = (sizeof(a) / 4)-1;
	quickSort(a, m, n);
	for (int i = 0; i < 10; i++)
	{
		cout << a[i] << " ";
	}
}
void quickSort(int a[], int m, int n)
{
	if (m < n)
	{
		int q = partion(a, m, n);
		quickSort(a, m, q );
		quickSort(a, q + 1, n);
	}
}
int partion(int a[], int m, int n)
{
	int key=m;
	int j= n,i=m;
	int temp1, temp2;
	while (i != j)
	{
		while (a[j] > a[key] && i < j)
		{
			--j;//j先动 
		}
		
		while ((a[i] < a[key]) && (i < j))
		{
			++i;//i后动 
		}
		if (i < j)
		{
			temp1 = a[j];
			a[j] = a[i];
			a[i] = temp1;//交换ij位置的数 
		}
	}
		temp2 = a[key];
		a[key] = a[i];
		a[i] = temp2;//交换pivot 
		return i;
}
```

## C++结构体与类的区别

- C的结构体和C++结构体的区别
  - C的结构体内不允许有函数存在，C++允许有内部成员函数，且允许该函数是虚函数。所以C的结构体是**没有构造函数、析构函数、和this指针**的。
  - C的结构体对内部成员变量的访问权限只能是**public**，而C++允许**public,protected,private**三种。
  - C语言的结构体是**不可以继承**的，C++的结构体是**可以**从其他的结构体或者类继承过来的。
  - C++中struct可以定义函数，但是C语言中struct**只可以定义函数指针**。
  - C语言中sizeof( struct )，会把定义的函数指针计算大小；C++中sizeof( struct ) 和 sizeof( class )都不会计算函数的大小，只会计算成员变量的大小。
  - 以上都是表面的区别，实际区别就是面向过程和面向对象编程思路的区别：
    - C的结构体只是把数据变量给包裹起来了，并不涉及算法
    - 而C++是把数据变量及对这些数据变量的相关算法给封装起来，并且给对这些数据和类不同的访问权限。

C语言中是没有类的概念的，但是C语言可以通过结构体内创建函数指针实现面向对象思想。

- C++的结构体和C++类的区别
  - C++结构体内部成员变量及成员函数默认的访问级别是**public**,而C++类的内部成员变量及成员函数的默认访问级别是**private**。
  - C++结构体的继承默认是**public**，而C++类的继承默认是**private**。
    

## 二叉树的遍历方法

- 先序遍历

```c++
void preOrder(TreeNode* root)
{
    visit(root);
    preOrder(root->left);
    preOrder(root->right);
}
```



- 中序遍历
- 后序遍历

上面的方法都是借助递归完成

- 借助栈完成

```c++
//中序遍历
void inOrder(TreeNode* root)
{
    stack<TreeNode*> st;
    
}
```

- 层次遍历

```c++
void levelOrder(TreeNode* root) 
{
        queue<TreeNode*> q;
        q.push(root);
        TreeNode* temp = new TreeNode();
        while(!q.empty())
        {
            temp = q.front();
            q.pop();
            visit(temp);
            if(temp->left != NULL)
                q.push(temp->left);
            if(temp->right != NULL)
                q.push(temp->right);
        }
}
```

## 二叉排序树

- 插入节点

```c++
int treeInsert(TreeNode* root, int key)
{
    if (root == NULL)
    {
        root = (TreeNode*)malloc(sizeof(TreeNode*));
        root->val = key;
        root->left = root->right = NULL;
        return 1;
    }
    else if (key == root->val)
        return 0;
    else if (key < root->val)
		return treeInsert(root->left, key);
    else
        return treeInsert(root->right, key);
}
```

- 删除节点

```c++

```


















