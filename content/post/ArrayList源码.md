---
title: "ArrayList源码"
date: 2019-07-09T20:58:54+08:00
categories: ["Java", "源码"]
tags: ["Java", "源码"]
author: CoderLeftEar
draft: true
---

# ArrayList源码实现
---

> 1、基本功能
```
/**
 * ArrayList基本实现
 * 
 * @author Mr.Wang
 *
 */
public class ArrayList01 {
	//存放数据
	private Object[] elementData;
	//大小
	private int size;
	//默认大小
	private static final int DEFAULT_CAPACITY = 10;
	
	//elementData默认大小
	public ArrayList01() {
		elementData = new Object[DEFAULT_CAPACITY];
	}
	
	//elementData自定义大小
	public ArrayList01(int capacity) {
		elementData = new Object[capacity];
	}
	
	//添加数据的方法
	public void add(Object obj) {
		elementData[size ++] = obj;
	}
	
	@Override
	public String toString() {
		//将elementData中的数据放到sb中
		StringBuilder sb = new StringBuilder();
		
		sb.append("[");
		for(int i = 0; i < size; i ++) {
			sb.append(elementData[i] + ",");
		}
		//将最后一个','替换为']'
		sb.setCharAt(sb.length()-1, ']');
		
		return sb.toString();
	}
	
	public static void main(String[] args) {
		
		ArrayList01 list = new ArrayList01();
		
		list.add("a");
		list.add("b");
		list.add("c");
		
		System.out.println(list);
	}
	
}
```

输出结果：
```
[a,b,c]
```

> 2、使用泛型
```
/**
 * ArrayList基本实现
 * 使用泛型
 * 
 * @author Mr.Wang
 *
 */
public class ArrayList02<E> {
	/*                   |此处             */
	//存放数据
	private Object[] elementData;
	//大小
	private int size;
	//默认大小
	private static final int DEFAULT_CAPACITY = 10;
	
	//elementData默认大小
	public ArrayList02() {
		elementData = new Object[DEFAULT_CAPACITY];
	}
	
	//elementData自定义大小
	public ArrayList02(int capacity) {
		elementData = new Object[capacity];
	}
	
	//添加数据的方法
	/*              |此处             */
	public void add(E element) {
		elementData[size ++] = element;
	}
	
	
	@Override
	public String toString() {
		//将elementData中的数据放到sb中
		StringBuilder sb = new StringBuilder();
		
		sb.append("[");
		for(int i = 0; i < size; i ++) {
			sb.append(elementData[i] + ",");
		}
		//将最后一个','替换为']'
		sb.setCharAt(sb.length()-1, ']');
		
		return sb.toString();
	}
	
	public static void main(String[] args) {
		
		ArrayList02<String> list = new ArrayList02<>();
		
		list.add("a");
		list.add("b");
		list.add("c");
		
		System.out.println(list);
	}
}
```

输出结果：
```
[a,b,c]
```


> 3、数组扩容
```
/**
 * ArrayList基本实现
 * 使用泛型
 * 数组扩容
 * 
 * @author Mr.Wang
 *
 */
public class ArrayList03<E> {
	/*                   |此处             */
	//存放数据
	private Object[] elementData;
	//大小
	private int size;
	//默认大小
	private static final int DEFAULT_CAPACITY = 10;
	
	//elementData默认大小
	public ArrayList03() {
		elementData = new Object[DEFAULT_CAPACITY];
	}
	
	//elementData自定义大小
	public ArrayList03(int capacity) {
		elementData = new Object[capacity];
	}
	
	//添加数据的方法
	/*              |此处             */
	public void add(E element) {
		//达到条件扩容
		if(size == elementData.length) {
			//扩容操作
			//创建一个新数组
			Object[] newArray = new Object[elementData.length + (elementData.length>>1)];
			//将数据拷贝到newArray中
			System.arraycopy(elementData, 0, newArray, 0, elementData.length);
			elementData = newArray;
		}
		
		elementData[size ++] = element;
	}
	
	@Override
	public String toString() {
		//将elementData中的数据放到sb中
		StringBuilder sb = new StringBuilder();
		
		sb.append("[");
		for(int i = 0; i < size; i ++) {
			sb.append(elementData[i] + ",");
		}
		//将最后一个','替换为']'
		sb.setCharAt(sb.length()-1, ']');
		
		return sb.toString();
	}
	
	public static void main(String[] args) {
		
		ArrayList03<String> list = new ArrayList03<>();
		
		for(int i = 0; i < 40; i ++) {
			list.add("a-" + i);
		}
		
		System.out.println(list);
	}
	
}
```

输出结果：
```
[a-0,a-1,a-2,a-3,a-4,a-5,a-6,a-7,a-8,a-9,a-10,a-11,a-12,a-13,a-14,a-15,a-16,a-17,a-18,a-19,a-20,a-21,a-22,a-23,a-24,a-25,a-26,a-27,a-28,a-29,a-30,a-31,a-32,a-33,a-34,a-35,a-36,a-37,a-38,a-39]
```


> 4、elementData初始大小设置、index下标设置、越界异常处理
```
/**
 * ArrayList基本实现
 * 使用泛型
 * 数组扩容
 * elementData初始大小设置、index下标设置
 * 
 * @author Mr.Wang
 *
 */
public class ArrayList04<E> {
	//存放数据
	private Object[] elementData;
	//大小
	private int size;
	//默认大小
	private static final int DEFAULT_CAPACITY = 10;
	
	//elementData默认大小
	public ArrayList04() {
		elementData = new Object[DEFAULT_CAPACITY];
	}
	
	//elementData自定义大小
	public ArrayList04(int capacity) {
		if(capacity < 0) {
			throw new IllegalArgumentException("elementData数组大小异常:" + capacity);
		} else if(capacity == 0) {
			elementData = new Object[DEFAULT_CAPACITY];
		} else {
			elementData = new Object[capacity];
		}
	}
	
	//判断索引值是否异常
	private void rangeCheck(int index) {
        if (index < 0 || index >= size - 1)
            throw new IndexOutOfBoundsException("索引值异常：Index: "+index);
    }
	
	//添加数据的方法
	public void add(E element) {
		//达到条件扩容
		if(size == elementData.length) {
			//扩容操作
			//创建一个新数组
			Object[] newArray = new Object[elementData.length + (elementData.length>>1)];
			//将数据拷贝到newArray中
			System.arraycopy(elementData, 0, newArray, 0, elementData.length);
			elementData = newArray;
		}
		
		elementData[size ++] = element;
	}
	
	public E get(int index) {
		rangeCheck(index);
		return (E)elementData[index];
	}
	
	public E set(int index, E element) {
		rangeCheck(index);
		//获取未修改的数据
		E oldElement = (E)elementData[index];
		elementData[index] = element;
		//返回修改前的数据
		return oldElement;
	}
	
	@Override
	public String toString() {
		//将elementData中的数据放到sb中
		StringBuilder sb = new StringBuilder();
		
		sb.append("[");
		for(int i = 0; i < size; i ++) {
			sb.append(elementData[i] + ",");
		}
		//将最后一个','替换为']'
		sb.setCharAt(sb.length()-1, ']');
		
		return sb.toString();
	}
	
	public static void main(String[] args) {
		
		ArrayList04<String> list = new ArrayList04<>();
		
		for(int i = 0; i < 40; i ++) {
			list.add("a-" + i);
		}
		
		System.out.println(list);
		System.out.println(list.set(-10, "10-10"));
		System.out.println(list.get(10));
	}
	
}
```

输出结果：
```
[a-0,a-1,a-2,a-3,a-4,a-5,a-6,a-7,a-8,a-9,a-10,a-11,a-12,a-13,a-14,a-15,a-16,a-17,a-18,a-19,a-20,a-21,a-22,a-23,a-24,a-25,a-26,a-27,a-28,a-29,a-30,a-31,a-32,a-33,a-34,a-35,a-36,a-37,a-38,a-39]
Exception in thread "main" java.lang.IndexOutOfBoundsException: 索引值异常：Index: -10
	at com.wang.arraylist.ArrayList04.rangeCheck(ArrayList04.java:39)
	at com.wang.arraylist.ArrayList04.set(ArrayList04.java:63)
	at com.wang.arraylist.ArrayList04.main(ArrayList04.java:95)

```


> 5、添加remove、isEmpty等方法
```
/**
 * ArrayList基本实现
 * 使用泛型
 * 数组扩容
 * elementData初始大小设置、index下标设置
 * 
 * @author Mr.Wang
 *
 */
public class ArrayList05<E> {
	//存放数据
	private Object[] elementData;
	//大小
	private int size;
	//默认大小
	private static final int DEFAULT_CAPACITY = 10;
	
	//elementData默认大小
	public ArrayList05() {
		elementData = new Object[DEFAULT_CAPACITY];
	}
	
	//elementData自定义大小
	public ArrayList05(int capacity) {
		if(capacity < 0) {
			throw new IllegalArgumentException("elementData数组大小异常:" + capacity);
		} else if(capacity == 0) {
			elementData = new Object[DEFAULT_CAPACITY];
		} else {
			elementData = new Object[capacity];
		}
	}
	
	//判断索引值是否异常
	private void rangeCheck(int index) {
        if (index < 0 || index > size - 1)
            throw new IndexOutOfBoundsException("索引值异常：Index: "+index);
    }
	
	//添加数据的方法
	public void add(E element) {
		//达到条件扩容
		if(size == elementData.length) {
			//扩容操作
			//创建一个新数组
			Object[] newArray = new Object[elementData.length + (elementData.length>>1)];
			//将数据拷贝到newArray中
			System.arraycopy(elementData, 0, newArray, 0, elementData.length);
			elementData = newArray;
		}
		
		elementData[size ++] = element;
	}
	
	public E get(int index) {
		rangeCheck(index);
		return (E)elementData[index];
	}
	
	public E set(int index, E element) {
		rangeCheck(index);
		//获取未修改的数据
		E oldElement = (E)elementData[index];
		elementData[index] = element;
		//返回修改前的数据
		return oldElement;
	}
	
	public void remove(E element) {
		//循环，数据对比
		for(int i = 0; i < size; i ++) {
			if(element.equals(get(i))) {
				//查找到，根据下标删除
				remove(i);
			}
		}
	}
	
	public void remove(int index) {
		rangeCheck(index);
		
		int numMoved = elementData.length - index - 1;
		if(numMoved > 0) {
			System.arraycopy(elementData, index + 1, elementData, index, numMoved);
		}
		
		elementData[-- size] = null;
		
	}
	
	public int size() {
		return size;
	}
	
	public boolean isEmpty() {
		return size == 0 ? true : false;
	}

	@Override
	public String toString() {
		//将elementData中的数据放到sb中
		StringBuilder sb = new StringBuilder();
		
		sb.append("[");
		for(int i = 0; i < size; i ++) {
			sb.append(elementData[i] + ",");
		}
		//将最后一个','替换为']'
		sb.setCharAt(sb.length()-1, ']');
		
		return sb.toString();
	}
	
	public static void main(String[] args) {
		
		ArrayList05<String> list = new ArrayList05<>();
		
		for(int i = 0; i < 40; i ++) {
			list.add("a" + i);
		}
		
		System.out.println(list);
		System.out.println(list.set(10, "10----10"));
		System.out.println(list.get(10));
		list.remove(0);
		System.out.println(list);
		list.remove("a1");
		System.out.println(list);
		System.out.println(list.size());
		System.out.println(list.isEmpty());
	}
	
}
```

输出结果：
```
a10
10----10
[a1,a2,a3,a4,a5,a6,a7,a8,a9,10----10,a11,a12,a13,a14,a15,a16,a17,a18,a19,a20,a21,a22,a23,a24,a25,a26,a27,a28,a29,a30,a31,a32,a33,a34,a35,a36,a37,a38,a39]
[a2,a3,a4,a5,a6,a7,a8,a9,10----10,a11,a12,a13,a14,a15,a16,a17,a18,a19,a20,a21,a22,a23,a24,a25,a26,a27,a28,a29,a30,a31,a32,a33,a34,a35,a36,a37,a38,a39]
38
false
```


