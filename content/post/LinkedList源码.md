---
title: "LinkedList源码"
date: 2019-07-09T20:59:13+08:00
author: CoderLeftEar
categories: ["Java", "源码"]
tags: ["Java", "源码"]
draft: true
---

# LinkedList源码实现
---

> Node类实现
```
public class Node {
	
	Node previous;
	Node next;
	Object element;
	
	public Node(Node previous, Node next, Object element) {
		this.previous = previous;
		this.next = next;
		this.element = element;
	}
	
	public Node(Object element) {
		this.element = element;
	}
}
```


> 1、添加、遍历的链表
```
/**
 * 添加、遍历的链表
 * 
 * @author Mr.Wang
 *
 */
public class LinkedList01 {
	
	private Node first;
	private Node last;
	private int size;
	
	/**
	 * 添加数据
	 * 
	 * @param obj
	 */
	public void add(Object obj) {
		Node node = new Node(obj);
		
		if(first == null) {
			//第一个节点为空，那么node节点就为第一个节点，并且前后都为null
			node.previous = null;
			node.next = null;
			//第一个和最后一个都为node节点
			first = node;
			last = node;
		} else {
			//存在第一个节点，那么插入的node的previous指向last，node.next为null
			node.previous = last;
			node.next = null;
			//将原来最后一个节点的last.next指向新添加的node节点
			last.next = node;
			//last将变为新添加到那个节点
			last = node;
		}
		
	}
	
	/**
	 * 遍历元素
	 */
	@Override
	public String toString() {
		StringBuilder sb = new StringBuilder("[");
		
		Node temp = first;
		while(temp != null) {
			sb.append(temp.element + ",");
			temp = temp.next;
		}
		sb.setCharAt(sb.length() - 1, ']');

		return sb.toString();
	}
	
	public static void main(String[] args) {
		
		LinkedList01 list = new LinkedList01();
		
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


> 2、get方法，add方法
```
/**
 * 添加、遍历的链表
 * get方法，add方法
 * 
 * @author Mr.Wang
 *
 */
public class LinkedList02 {
	
	private Node first;
	private Node last;
	private int size;
	
	
	
	LinkedList a;
	
	private void checkElementIndex(int index) {
        if (index < 0 || index > size - 1)
            throw new IndexOutOfBoundsException("下标异常：" + index);
    }
	
	
	public Object get(int index) {
		//检查索引
		checkElementIndex(index);
		
		Node temp = null;
		//index小于size>>1,执行这个，提高效率
		if(index <= (size>>1)) {
			temp = first;
			for(int i = 0; i <= (size>>1); i ++) {
				temp = temp.next;
			}
		} else {
			temp = last;
			for(int i = (size - 1); i > index; i --) {
				temp = temp.previous;
			}
		}
		
		return temp.element;
	}
	
	/**
	 * 添加数据
	 * 
	 * @param obj
	 */
	public void add(Object obj) {
		Node node = new Node(obj);
		
		if(first == null) {
			//第一个节点为空，那么node节点就为第一个节点，并且前后都为null
			node.previous = null;
			node.next = null;
			//第一个和最后一个都为node节点
			first = node;
			last = node;
		} else {
			//存在第一个节点，那么插入的node的previous指向last，node.next为null
			node.previous = last;
			node.next = null;
			//将原来最后一个节点的last.next指向新添加的node节点
			last.next = node;
			//last将变为新添加到那个节点
			last = node;
		}
		
		size ++;
		
	}
	
	/**
	 * 遍历元素
	 */
	@Override
	public String toString() {
		StringBuilder sb = new StringBuilder("[");
		
		Node temp = first;
		while(temp != null) {
			sb.append(temp.element + ",");
			temp = temp.next;
		}
		sb.setCharAt(sb.length() - 1, ']');

		return sb.toString();
	}
	
	
	public static void main(String[] args) {
		
		LinkedList02 list = new LinkedList02();
		
		list.add("a");
		list.add("b");
		list.add("c");
		list.add("d");
		list.add("e");
		list.add("f");
		
		System.out.println(list);
		
		System.out.println(list.get(5));
		
	}
	
}
```

输出结果：
```
[a,b,c,d,e,f]
f
```


> 3、remove方法、add插入方法
```
/**
 * 添加、遍历的链表
 * get方法，add方法
 * remove方法
 * add插入方法
 * 
 * @author Mr.Wang
 *
 */
public class LinkedList03 {
	
	private Node first;
	private Node last;
	private int size;
	
	
	
	//判断索引是否合法
	private void checkElementIndex(int index) {
        if (index < 0 || index > size - 1)
            throw new IndexOutOfBoundsException("下标异常：" + index);
    }
	
	
	public Object remove(int index) {
		//找到需要删除的节点
		Node temp = getNode(index);
		
		if(temp != null) {
			//需要删除的节点的前后节点
			Node front = temp.previous;
			Node behind = temp.next;
			//
			if(front != null) {
				front.next = behind;
			}
			if(behind != null) {
				behind.previous = front;
			}
			
			if(index == 0) {
				first = behind;
			}
			if(index == size - 1) {
				last = front;
			}
			
			size --;
			
		}
		
		return temp.element;
		
	}
	
	public Object get(int index) {
		//检查索引
		checkElementIndex(index);
		//查找到的节点
		Node temp = getNode(index);
		//返回数据
		return temp != null ? temp.element : null;
		
	}
	
	private Node getNode(int index) {
		Node temp = null;
		//index小于size>>1,执行这个，提高效率
		if(index <= (size>>1)) {
			temp = first;
			for(int i = 0; i < index; i ++) {
				temp = temp.next;
			}
		} else {
			temp = last;
			for(int i = (size - 1); i > index; i --) {
				temp = temp.previous;
			}
		}
		
		return temp;
	}
	
	/**
	 * 添加数据
	 * 
	 * @param obj
	 */
	public void add(Object obj) {
		Node node = new Node(obj);
		
		if(first == null) {
			//第一个节点为空，那么node节点就为第一个节点，并且前后都为null
			node.previous = null;
			node.next = null;
			//第一个和最后一个都为node节点
			first = node;
			last = node;
		} else {
			//存在第一个节点，那么插入的node的previous指向last，node.next为null
			node.previous = last;
			node.next = null;
			//将原来最后一个节点的last.next指向新添加的node节点
			last.next = node;
			//last将变为新添加到那个节点
			last = node;
		}
		
		size ++;
	}
	
	public void add(int index, Object obj) {
		//需要插入的节点
		Node newNode = new Node(obj);
		//index原位置节点
		Node temp = getNode(index);
		
		if(index == 0) {
			newNode.next = first;
			first.previous = newNode;
			first = newNode;
		}
		
//		if(index == (size - 1)) {
//			newNode.previous = last;
//			last.next = newNode;
//			last = newNode;
//		}
		
		if(temp != null) {
			Node front = temp.previous;
			
			front.next = newNode;
			newNode.previous = front;
			
			newNode.next = temp;
			temp.previous = newNode;	
		}
		
		
	}
	
	/**
	 * 遍历元素
	 */
	@Override
	public String toString() {
		StringBuilder sb = new StringBuilder("[");
		
		Node temp = first;
		while(temp != null) {
			sb.append(temp.element + ",");
			temp = temp.next;
		}
		sb.setCharAt(sb.length() - 1, ']');

		return sb.toString();
	}
	
	
	public static void main(String[] args) {
		
		LinkedList03 list = new LinkedList03();
		
		list.add("a");
		list.add("b");
		list.add("c");
		list.add("d");
		list.add("e");
		list.add("f");
		
		System.out.println(list);
		
		list.add(5, "456");
		list.add("789");
		
		System.out.println(list);
	}
	
}
```

输出结果：
```
[a,b,c,d,e,f]
[a,b,c,d,e,456,f,789]
```


> 4、添加泛型
```
/**
 * 添加、遍历的链表
 * get方法，add方法
 * remove方法
 * add插入方法
 * 泛型
 * 
 * @author Mr.Wang
 *
 */
public class LinkedList04<E> {
	
	private Node first;
	private Node last;
	private int size;
	
	
	//判断索引是否合法
	private void checkElementIndex(int index) {
        if (index < 0 || index > size - 1)
            throw new IndexOutOfBoundsException("下标异常：" + index);
    }
	
	
	public E remove(int index) {
		//检查索引
		checkElementIndex(index);
		//找到需要删除的节点
		Node temp = getNode(index);
		
		if(temp != null) {
			//需要删除的节点的前后节点
			Node front = temp.previous;
			Node behind = temp.next;
			//
			if(front != null) {
				front.next = behind;
			}
			if(behind != null) {
				behind.previous = front;
			}
			
			if(index == 0) {
				first = behind;
			}
			if(index == size - 1) {
				last = front;
			}
			
			size --;
			
		}
		
		return (E)temp.element;
		
	}
	
	public E get(int index) {
		//检查索引
		checkElementIndex(index);
		//查找到的节点
		Node temp = getNode(index);
		//返回数据
		return temp != null ? (E)temp.element : null;
		
	}
	
	private Node getNode(int index) {
		//检查索引
		checkElementIndex(index);
		Node temp = null;
		//index小于size>>1,执行这个，提高效率
		if(index <= (size>>1)) {
			temp = first;
			for(int i = 0; i < index; i ++) {
				temp = temp.next;
			}
		} else {
			temp = last;
			for(int i = (size - 1); i > index; i --) {
				temp = temp.previous;
			}
		}
		
		return temp;
	}
	
	/**
	 * 添加数据
	 * 
	 * @param element
	 */
	public void add(E element) {
		Node node = new Node(element);
		
		if(first == null) {
			//第一个节点为空，那么node节点就为第一个节点，并且前后都为null
			node.previous = null;
			node.next = null;
			//第一个和最后一个都为node节点
			first = node;
			last = node;
		} else {
			//存在第一个节点，那么插入的node的previous指向last，node.next为null
			node.previous = last;
			node.next = null;
			//将原来最后一个节点的last.next指向新添加的node节点
			last.next = node;
			//last将变为新添加到那个节点
			last = node;
		}
		
		size ++;
	}
	
	public void add(int index, E element) {
		//检查索引
		checkElementIndex(index);
		//需要插入的节点
		Node newNode = new Node(element);
		//index原位置节点
		Node temp = getNode(index);
		
		if(index == 0) {
			newNode.next = first;
			first.previous = newNode;
			first = newNode;
		}
		
//		if(index == (size - 1)) {
//			newNode.previous = last;
//			last.next = newNode;
//			last = newNode;
//		}
		
		if(temp != null) {
			Node front = temp.previous;
			
			front.next = newNode;
			newNode.previous = front;
			
			newNode.next = temp;
			temp.previous = newNode;	
		}
		
		
	}
	
	/**
	 * 遍历元素
	 */
	@Override
	public String toString() {
		StringBuilder sb = new StringBuilder("[");
		
		Node temp = first;
		while(temp != null) {
			sb.append(temp.element + ",");
			temp = temp.next;
		}
		sb.setCharAt(sb.length() - 1, ']');

		return sb.toString();
	}
	
	
	public static void main(String[] args) {
		
		LinkedList04<String> list = new LinkedList04<>();
		
		list.add("a");
		list.add("b");
		list.add("c");
		list.add("d");
		list.add("e");
		list.add("f");
		
		System.out.println(list);
		
		list.add(5, "456");
		list.add("789");
		
		System.out.println(list);
		
		System.out.println(list.remove(0));
	}
	
}
```

输出结果：
```
[a,b,c,d,e,f]
[a,b,c,d,e,456,f,789]
a
```

