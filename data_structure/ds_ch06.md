title: 数据结构（006）-快速排序
date: 2014-11-22 18:34:26
categories:
- 数据结构
tags:
---
```
int QuickSort(int R[], int l, int r) {
  if ( NULL == R || l < 0 || r < 0 )
    return -1;
  QuickSortInternal(R, l, r);
  return 0;
}

void QuickSortInternal ( int R[], int l, int r ) {
  int temp;
  int i = l, j = r;
  if (l < r ) {
    temp = R[l];
	while ( i != j ) {
	  while ( j > i && R[j] > temp ) --j;
	  if ( i < j ) {
	    R[i] = R[j];
		++i;
	  }
	  while ( i < j && R[i] < temp ) ++i;
	  if ( i < j ) {
	    R[j] = R[i];
		--j;
	  }
	}
	R[i] = temp;
	QuickSort(R, l, i-1);
	QuickSort(R, i+1, r);
  }
}
```
