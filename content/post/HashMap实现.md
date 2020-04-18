---
title: "HashMap实现"
date: 2019-08-04T11:30:48+08:00
author: CoderLeftEar
categories: ["Java", "源码"]
tags: ["Java", "源码"]
draft: true
---

# HashMap实现
---

> 未添加泛型的Node类

```
public class Node {
	
	int hash;
	Object key;
	Object value;
	Node next;
	
}
```

> 1、put、myHash、tostring

```
/**
 * put、myHash、tostring
 * 
 * @author Mr.Wang
 *
 */
public class HashMap01 {
	
	Node[] table;	//桶位数组 bucket array
	int size;
	
	public HashMap01() {
		table = new Node[16];
	}
	
	public void put(Object key, Object value) {
		Node newNode = new Node();
		newNode.hash = myHash(key.hashCode(), table.length);
		newNode.key = key;
		newNode.value = value;
		newNode.next = null;
		
		Node temp = table[newNode.hash];
		//正在遍历的最后一个元素
		Node lastNode = null;
		//记录key是否重复
		boolean keyRepeat = false;
		
		if(temp == null) {
			//table数组元素为空，就直接将newNode放进去
			table[newNode.hash] = newNode;
			size ++;
		} else {
			//table数组元素不为空，遍历链表，有键重复，就替换掉，没有重复，放最后 
			while(temp != null) {
				//key重复，直接将value替换
				if(temp.key.equals(key)) {
					//key重复变为true
					keyRepeat = true;
					//覆盖key相同的前者value
					temp.value = value;
					break;
				} else {
					//记录table[]桶位数组其中一个的链表的最后一位
					lastNode = temp;
					//temp不为空就一直向后遍历，知道最后一位
					temp = temp.next;
				}
			}
			//keyRepeat = false 就将newNode链表的最后一位
			if(!keyRepeat) {
				lastNode.next = newNode;
				size ++;
			}
		}
	}
	
	//返回与运算符 % 计算结果相同的余数
	public int myHash(int value, int length) {
		return value & (length - 1);
	}
	
	@Override
	public String toString() {
		StringBuilder sb = new StringBuilder("{");
		//记录每个桶位数组中链表的第一个Node
		Node temp = null;
		//循环打印桶位数组中的数据
		for(int i = 0; i < table.length; i ++) {
			temp = table[i];
			//如果桶位数组不为空,打印数据
			if(temp != null) {
				sb.append("[");
				//当temp到达链表最后一位，跳出循环
				while(temp != null) {
					sb.append(temp.key + "=" + temp.value + ", ");
					temp = temp.next;
				}
				//删除每个桶位数组最后两位的 ", " 符号
				sb.deleteCharAt(sb.length() - 1);
				sb.deleteCharAt(sb.length() - 1);
				
				sb.append("], ");
			}
		}
		
		sb.deleteCharAt(sb.length() - 1);
		sb.setCharAt(sb.length() - 1, '}');
	
		return sb.toString();
	}
	
	
	
	public static void main(String[] args) {
		
		HashMap01 map = new HashMap01();
		
		map.put(10, "aaa");
		map.put(20, "bbb");
		map.put(30, "ccc");
		
		map.put(26, "a---1");
		map.put(42, "b---1");
		
		System.out.println(map);
		
		
	}
	
}
```

> 输出结果：

```
{[20=bbb], [10=aaa, 26=a---1, 42=b---1], [30=ccc]}
```


> 2、get查找

```
/**
 * put、myHash、tostring
 * get查找
 * 
 * @author Mr.Wang
 *
 */
public class HashMap02 {
	
	Node[] table;	//桶位数组 bucket array
	int size;
	
	public HashMap02() {
		table = new Node[16];
	}
	
	public Object get(Object key) {
		int hash = myHash(key.hashCode(), table.length);
		
		Node temp = table[hash];
		//返回get的到的值
		Object value = null;
		//先判断table的hash为是否为空，防止意外发生
		if(temp != null) {
			//当链表一直向后移,一直到temp.next为空时退出循环
			while(temp != null) {
				if(temp.key.equals(key)) {
					value = temp.value;
					break;
				} else {
					temp = temp.next;
				}
			}
		}
		
		return value;
	}
	
	public void put(Object key, Object value) {
		Node newNode = new Node();
		newNode.hash = myHash(key.hashCode(), table.length);
		newNode.key = key;
		newNode.value = value;
		newNode.next = null;
		
		Node temp = table[newNode.hash];
		//正在遍历的最后一个元素
		Node lastNode = null;
		//记录key是否重复
		boolean keyRepeat = false;
		
		if(temp == null) {
			//table数组元素为空，就直接将newNode放进去
			table[newNode.hash] = newNode;
			size ++;
		} else {
			//table数组元素不为空，遍历链表，有键重复，就替换掉，没有重复，放最后 
			while(temp != null) {
				//key重复，直接将value替换
				if(temp.key.equals(key)) {
					//key重复变为true
					keyRepeat = true;
					//覆盖key相同的前者value
					temp.value = value;
					break;
				} else {
					//记录table[]桶位数组其中一个的链表的最后一位
					lastNode = temp;
					//temp不为空就一直向后遍历，知道最后一位
					temp = temp.next;
				}
			}
			//keyRepeat = false 就将newNode链表的最后一位
			if(!keyRepeat) {
				lastNode.next = newNode;
				size ++;
			}
		}
	}
	
	//返回与运算符 % 计算结果相同的余数
	public int myHash(int value, int length) {
		return value & (length - 1);
	}
	
	@Override
	public String toString() {
		StringBuilder sb = new StringBuilder("{");
		
		
		for(int i = 0; i < table.length; i ++) {
			Node temp = table[i];
			
			while(temp != null) {
				sb.append(temp.key + "=" + temp.value + ", ");
				temp = temp.next;
			}			
			
		}
		sb.setCharAt(sb.length() - 2, '}');
		
		return sb.toString();
	}
	
	
	
	public static void main(String[] args) {
		
		HashMap02 map = new HashMap02();
		
		map.put(10, "aaa");
		map.put(20, "bbb");
		map.put(30, "ccc");
		
		map.put(26, "a---1");
		map.put(42, "b---1");
		
		System.out.println(map);
		
		System.out.println(map.get(42));
		
	}
	
}
```

> 输出结果：

```
{20=bbb, 10=aaa, 26=a---1, 42=b---1, 30=ccc} 
b---1
```

---
---
---

> 添加泛型的Node2类

```
public class Node2<K,V> {
	
	int hash;
	K key;
	V value;
	Node2<K,V> next;

}
```


> 3、添加泛型

```
/**
 * put、myHash、tostring
 * get查找
 * 添加泛型
 * 
 * @author Mr.Wang
 *
 */
public class HashMap03<K,V> {
	
	Node2[] table;	//桶位数组 bucket array
	int size;
	
	public HashMap03() {
		table = new Node2[16];
	}
	
	public Object get(K key) {
		int hash = myHash(key.hashCode(), table.length);
		
		Node2 temp = table[hash];
		//返回get的到的值
		V value = null;
		//先判断table的hash为是否为空，防止意外发生
		if(temp != null) {
			//当链表一直向后移,一直到temp.next为空时退出循环
			while(temp != null) {
				if(temp.key.equals(key)) {
					value = (V)temp.value;
					break;
				} else {
					temp = temp.next;
				}
			}
		}
		
		return value;
	}
	
	public void put(K key, V value) {
		Node2 newNode2 = new Node2();
		newNode2.hash = myHash(key.hashCode(), table.length);
		newNode2.key = key;
		newNode2.value = value;
		newNode2.next = null;
		
		Node2 temp = table[newNode2.hash];
		//正在遍历的最后一个元素
		Node2 lastNode2 = null;
		//记录key是否重复
		boolean keyRepeat = false;
		
		if(temp == null) {
			//table数组元素为空，就直接将newNode2放进去
			table[newNode2.hash] = newNode2;
			size ++;
		} else {
			//table数组元素不为空，遍历链表，有键重复，就替换掉，没有重复，放最后 
			while(temp != null) {
				//key重复，直接将value替换
				if(temp.key.equals(key)) {
					//key重复变为true
					keyRepeat = true;
					//覆盖key相同的前者value
					temp.value = value;
					break;
				} else {
					//记录table[]桶位数组其中一个的链表的最后一位
					lastNode2 = temp;
					//temp不为空就一直向后遍历，知道最后一位
					temp = temp.next;
				}
			}
			//keyRepeat = false 就将newNode2链表的最后一位
			if(!keyRepeat) {
				lastNode2.next = newNode2;
				size ++;
			}
		}
	}
	
	//返回与运算符 % 计算结果相同的余数
	public int myHash(int value, int length) {
		return value & (length - 1);
	}
	
	@Override
	public String toString() {
		StringBuilder sb = new StringBuilder("{");
		
		
		for(int i = 0; i < table.length; i ++) {
			Node2 temp = table[i];
			
			while(temp != null) {
				sb.append(temp.key + "=" + temp.value + ", ");
				temp = temp.next;
			}			
			
		}
		sb.setCharAt(sb.length() - 2, '}');
		
		return sb.toString();
	}
	
	
	
	public static void main(String[] args) {
		
		HashMap03<Integer, String> map = new HashMap03<Integer, String>();
		
		map.put(10, "aaa");
		map.put(20, "bbb");
		map.put(30, "ccc");
		
		map.put(26, "a---1");
		map.put(42, "b---1");
		
		System.out.println(map);
		
		System.out.println(map.get(42));
		System.out.println(map.get(26));
		
	}
	
}
```

> 输出结果：

```
{20=bbb, 10=aaa, 26=a---1, 42=b---1, 30=ccc} 
b---1
a---1
```



