---
title: 4.2.黑科技-STL的神奇应用
tags:
  - ACM
  - 数学模版
abbrlink: f661a019
date: 2020-03-12 09:44:16
top:
---

## 4.2. STL的神奇应用

### 4.2.1. 哈希表
<!--more-->
#### 4.2.1.1. 手写哈希表

```cpp
// 用法和 unorder_map 一样（除了 tb[k]=p 改成 tb.add(k,p)）
// 支持 a = tb[tmp] 的用法
#define HASHMOD 233333
#define HASHSIZE 1000000
static struct HashTable 
{
    int head[HASHMOD];
    int key[HASHSIZE + 10], val[HASHSIZE + 10], nxt[HASHSIZE + 10], cnt;
    void clear() 
    {
        cnt = 0;
        memset(head, 0, sizeof(head));
    }
    bool count(const int k) 
    {
        for (int i = head[k % HASHMOD]; i; i = nxt[i])
            if (key[i] == k) return true;
        return false;
    }
    int operator[](const int k) 
    {
        for (int i = head[k % HASHMOD]; i; i = nxt[i])
            if (key[i] == k) return val[i];
        return 0;
    }
    void add(int k, int v) {
        int p = k % HASHMOD;
        nxt[++cnt] = head[p];
        key[cnt] = k;
        val[cnt] = v;
        head[p] = cnt;
    }
}tb;
```

#### 4.2.1.2. unordered-map哈希函数加速

```cpp
struct custom_hash
{
    static uint64_t splitmix64(uint64_t x)
    {
        // http://xorshift.di.unimi.it/splitmix64.c
        x += 0x9e3779b97f4a7c15;
        x = (x ^ (x >> 30)) * 0xbf58476d1ce4e5b9;
        x = (x ^ (x >> 27)) * 0x94d049bb133111eb;
        return x ^ (x >> 31);
    }

    size_t operator()(uint64_t x) const
    {
        static const uint64_t FIXED_RANDOM = 
            chrono::steady_clock::now().time_since_epoch().count();
        return splitmix64(x + FIXED_RANDOM);
    }
/*    
    这样写实测差别不大
    size_t operator()(uint64_t x) const
    {
        return splitmix64(x);
    }
*/    
};
unordered_map<int, int, custom_hash> um;
```

#### 4.2.1.3. pair<int, int> 的哈希函数重载

```cpp
struct hashfunc
{
    template<typename T, typename U>
    size_t operator() (const pair<T, U> &i) const
    {
        return hash<T>()(i.first) ^ hash<U>()(i.second);
    }
};
unordered_map< pair<int, int>, int , hashfunc > mp;
```
