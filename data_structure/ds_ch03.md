title: 数据结构（003）-直接插入排序
date: 2014-11-19 18:34:26
categories:
- 数据结构
tags:
---
```
int InsertSort( int R[], int n ) {
  if ( NULL == R || n < 0 )
    return -1;
  int i, j;
  int temp;
  for ( i=1; i != n-1; ++i ) {
    temp = R[i];
	j = i - 1;
	while ( j >= 0 && temp < R[j] ) {
	  R[j+1] = R[j];
	  --j;
	}
	R[j+1] = temp;
  }
  return 0;
}
```

>>最好时间复杂度：n （数组正序时）
>>最坏时间复杂度：n2（数组逆序时）
>>平均时间复杂度：n2
>>空间复杂度：1
