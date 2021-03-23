

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

- 































