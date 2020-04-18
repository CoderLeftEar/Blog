---
title: "Iterator遍历List、Set、Map"
date: 2019-08-04T11:25:11+08:00
author: CoderLeftEar
categories: ["Java"]
tags: ["Java"]
draft: true
---

# Iterator遍历List、Set、Map
---

> Iterator遍历List、Set、Map



```
public class Iterator01 {

	public static void main(String[] args) {
		/**
		 * Iterator遍历list或set，相同
		 */
		listIterator();
		
		/**
		 * Iterator遍历map
		 */
         //map.entrySet();
//		mapIterator01();
		
		//map.keySet();
//		mapIterator02();
		
		
	}

	public static void listIterator() {
		List<String> list = new ArrayList<String>();
		
		list.add("aaa");
		list.add("bbb");
		list.add("ccc");
		
		Iterator<String> it = list.iterator();
		
		while(it.hasNext()) {
			System.out.println(it.next());
		}
	}
	
	public static void mapIterator01() {
		Map<Integer, String> map = new HashMap<>();
		
		map.put(20, "bb");
		map.put(30, "cc");
		map.put(10, "aa");
		
		Set<Entry<Integer, String>> set = map.entrySet();
		Iterator<Entry<Integer, String>> it = set.iterator();
		
		while(it.hasNext()) {
			Entry<Integer, String> temp = it.next();
			System.out.println(temp.getKey() + "---" + temp.getValue());
		}
	}
	
	public static void mapIterator02() {
		Map<Integer, String> map = new HashMap<>();
		
		map.put(20, "bb");
		map.put(30, "cc");
		map.put(10, "aa");
		
		Set<Integer> set = map.keySet();
		Iterator<Integer> it = set.iterator();
		
		while(it.hasNext()) {
			Integer key = it.next();
			System.out.println(key + "===" + map.get(key));
		}
	}
	
}
```
