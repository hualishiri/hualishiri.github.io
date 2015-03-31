title: 数据结构（005）-冒泡插入排序
date: 2014-11-21 18:34:26
categories:
- 数据结构
tags:
---
```
int BubbleSort(int R[], int n) {
  if ( NULL == R || n < 0 )
    return -1;
  int i, j, flag;
  int temp;
  for ( i=n-1; i!=0; --i ) {
    flag = 0;
	for ( j=0; j!=i; ++j ) {
	  if ( R[j] > R[j+1] ) {
	    temp = R[j];
	    R[j] = R[j+1];
	    R[j+1] = temp;
	    flag = 1;
	  }
	}
	if (!flag)
	  break;
  }
  return 0;
}
```
