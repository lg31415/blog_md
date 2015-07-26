title: "寻找n个整数中前最小的k个数.md"
date: 2015-04-28 09:12:01
tags: 面试
categories: 
---

面试题目：输入n个整数，输出其中最小的前k个数。

　　例如输入1，2，3，4，5，6，7和8这8个数字，则最小的3个数字为1，2，3。

　　分析：这道题最简单的思路莫过于把输入的n个整数排好序，然后输出前面k个数，这就是最小的前k个数。但是按照这种思路最好的时间复杂度为O(nlogn)，是否还有比这个更快的算法呢？

　　我们可以先创建一个大小为k的数据容器来存储最小的k个数字。接下来我们每次从输入的n个整数中读入一个数。如果容器中已有的数字少于k个，则直接把这次读入的整数放入容器之中；如果容器中已有k个数字了，也就是容器已满，此时我们不能再插入新的数字而只能替换已有的数字。我们找出这已有的k个数中最大值，然和拿这次待插入的整数和这个最大值进行比较。如果待插入的值比当前已有的最大值小，则用这个数替换替换当前已有的最大值；如果带插入的值比当前已有的最大值还要大，那么这个数不可能是最小的k个整数之一，因为我们容器内已经有k个数字比它小了，于是我们可以抛弃这个整数。

　　因此当容器满了之后，我们要做三件事情：一是在k个整数中找到最大数，二是有可能在这个容器中删除最大数，三是可能要插入一个新的数字，并保证k个整数依然是排序的。如果我们用一个二叉树来实现这个数据容器，那么我们能在O(logk)时间内实现这三步操作。因此对于n个输入数字而言，总的时间效率就是O(nlogk)。

　　我们可以选择用不同的二叉树来实现这个数据容器。由于我们每次都需要找到k个整数中的最大数字，我们很容易想到用最大堆。在最大堆中，根结点的值总是大于它的子树中任意结点的值。于是我们每次可以在O(1)得到已有的k个数字中的最大值，但需要O(logk)时间完成删除以及插入操作。下面我们用multiset来存储钱k个最小的数字。

```c
#include <iostream>
#include <vector>
#include <set>
#include <fstream>

// 过往记忆
// www.iteblog.com 

using namespace std;

void findTheKthValue(vector<long> &v, int k){
    vector<long>::iterator it;
    multiset<long, greater<long> > s;
    multiset<long, greater<long> >::iterator sit;
    s.clear();
    
    for(it = v.begin(); it != v.end(); it++){
        if(s.size() < k){
            s.insert(*it);
        }else{
            sit = s.begin();
            if(*sit > *it){
                s.erase(sit);
                s.insert(*it);
            }
        }
    }
    
    multiset<long, greater<long> >::reverse_iterator cit;
    int tempSize = s.size();
    int tempflags = 0;
    for(cit = s.rbegin(); cit != s.rend(); cit++){
        printf("%d", *cit);
        if(tempflags < tempSize - 1){
            printf(" ");
        }
        tempflags++;
    }
    
    cout << endl;
}

int main(){
    int n, k;
    vector<long>v;

    int i;
    long temp;
    //ifstream cin("test.txt");
    
    while(cin >> n >> k){
        if(n >= k){
            v.clear();
            for(i = 0; i < n; i++){
                scanf("%ld", &temp);
                v.push_back(temp);          
            }
            findTheKthValue(v, k);
        }
    }
    return 0;
} 
```