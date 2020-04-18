---
title: "DataStructure01-SparseArray"
date: 2019-09-03T21:01:19+08:00
author: CoderLeftEar
tags: ["DataStructure"]
categories: ["DataStructure"]
draft: true
---

# DataStructure01-SparseArray
------------------------

`原始数组`: 创建的放置棋子的数组棋盘
```
//定义棋盘数组
int[][] arr = new int[11][11];
//定义总棋子个数
int sum = 0;

//赋值
//0表示无子，1表示黑子，2表示白子
arr[1][0] = 1;
arr[2][1] = 2;
arr[3][2] = 2;

//循环遍历
for (int i = 0; i < arr.length; i ++) {
    for (int j = 0; j < arr[0].length; j ++) {
        System.out.print(arr[i][j] + "\t");
        //System.out.printf("%d\t", arr[i][j]);
        //判断是否是棋子，是就将sum++
        if (arr[i][j] != 0) {
            sum ++;
        }
    }
    System.out.println();
}
System.out.println("总数：" + sum);
```

`稀疏数组`: 将普通二维数组转化为稀疏数组
```
//定义稀疏数组
int[][] sparseArr = new int[sum + 1][3];

//给sparseArr[0]赋值，即原数组的大小及棋子个数
sparseArr[0][0] = arr.length;
sparseArr[0][1] = arr[0].length;
sparseArr[0][2] = sum;

//统计是否是棋子，有棋子就count++，并将棋子的位置和颜色赋给sparseArr
int count = 0;

//遍历赋值稀疏数组，将棋子位置和棋子颜色赋进sparseArr数组
for (int i = 0; i < arr.length; i ++) {
    for (int j = 0; j < arr[0].length; j ++) {
        if (arr[i][j] != 0) {
            count ++;
            sparseArr[count][0] = i;
            sparseArr[count][1] = j;
            sparseArr[count][2] = arr[i][j];
        }
    }
}

//调用遍历方法
print(sparseArr);
```

`稀疏数组还原为数组`

```
//获取稀疏数组中的原数组长度，创建新的数组
int[][] chessArr = new int[sparseArr[0][0]][sparseArr[0][1]];

//遍历稀疏数组的行数，将每行的位置信息和颜色数据给新创建的chessArr
for (int i = 1; i < sparseArr.length; i ++) {
    chessArr[sparseArr[i][0]][sparseArr[i][1]] = sparseArr[i][2];
}

//调用遍历方法
print(chessArr);
```

------------------------------

**遍历稀疏数组的方法，将其封装起来，方便调用**

```
//遍历稀疏数组，封装
public static void print (int [][] arr) {
    for (int i = 0; i < arr.length; i ++) {
        for (int j = 0; j < arr[0].length; j ++) {
            System.out.print(arr[i][j] + "\t");
        }
        System.out.println();
    }
}
```


```
=============原始数组============
0	0	0	0	0	0	0	0	0	0	0	
1	0	0	0	0	0	0	0	0	0	0	
0	2	0	0	0	0	0	0	0	0	0	
0	0	2	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
总数：3

=============稀疏数组============
11	11	3	
1	0	1	
2	1	2	
3	2	2	

=============稀疏数组还原为数组============
0	0	0	0	0	0	0	0	0	0	0	
1	0	0	0	0	0	0	0	0	0	0	
0	2	0	0	0	0	0	0	0	0	0	
0	0	2	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
```







