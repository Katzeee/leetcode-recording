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

## string

```c++
const char* charElement = stringElement.c_str(); //转string为char指针
```

