title: 数据结构（004）-折半插入排序
date: 2014-11-20 18:34:26
categories:
- 数据结构
tags:
---
```
int BinInsertSort( int R[], int n ) {
  if ( NULL == R || n <= 1 )
    return -1;
  int i, j;
  int temp, low, high, mid;
  for ( i=1; i != n-1; ++i ) {
    temp = R[i];
	low = 0;
	high = i - 1;
	while ( low <= high ) {
	  mid = (log + high) / 2;
	  if ( R[mid] == temp )
	    break;
	  else if ( R[mid] > temp )
	    high = mid - 1;
	  else 
	    low = mid + 1;
	}
	for ( j=i-1; j!=high; --j )
	  R[j+1] = R[j];
	R[low] = temp;
  }
  return 0;
}
```
