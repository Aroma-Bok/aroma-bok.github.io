---
title: "[Java] keyset(), iterator() 메서드"
date: 2021-10-20 00:53:56 +0900
categories: ["Language", "Java"]
tags: ["entryset", "hashmap", "iterator", "keyset", "map"]
tistory_url: "https://aroma-bok.tistory.com/entry/Java-keyset-iterator-%EB%A9%94%EC%84%9C%EB%93%9C"
---
Map에 값을 출력하기 위해서 entrySet() 함수, keySet() 함수를 사용한다고 한다. 

**entrySet** 함수는 key와 value의 값을 모두 필요한 경우에 사용하고,

**keySet** 함수는 key 값만 필요한 경우에 사용한다고 한다.

```javascript
	public static void main(String[] args) {
		//방법 1: entrySet()
		Map<String, String> map = new HashMap<String, String>();
		map.put("key1", "val1");
		map.put("key2", "val2");
		map.put("key3", "val3");
		map.put("key4", "val4");
		map.put("key5", "val5");

		
		for (Map.Entry<String, String> entry : map.entrySet()) {
			System.out.println("[key]:" + entry.getKey() + ", [val]:" + entry.getValue());
			
		}
		System.out.println("------------------------------------------------------");
		
		// 방법 2 : keySet()

		for (String key : map.keySet()) {
			String value = map.get(key);
		    System.out.println("[key]:" + key + ", [val]:" + value);
		}    
        
결과
        
[key]:key1, [val]:val1
[key]:key2, [val]:val2
[key]:key5, [val]:val5
[key]:key3, [val]:val3
[key]:key4, [val]:val4
------------------------------------------------------
[key]:key1, [val]:val1
[key]:key2, [val]:val2
[key]:key5, [val]:val5
[key]:key3, [val]:val3
[key]:key4, [val]:val4
```

그리고 Map 컬렉션은 Iterator 인터페이스를 사용할 수 없다고 한다. 그래서 Iterator 인터페이스를 사용하기 위해서는

Map에 entrySet(), keySet() 메서드를 사용하여 Set객체를 반환받고 Iterator 인터페이스를 사용해야 한다고 한다.

```javascript
		// 방법 03 : entrySet().iterator()
		Iterator<Map.Entry<String, String>> iteratorE = map.entrySet().iterator();
		while (iteratorE.hasNext()) {
			Map.Entry<String, String> entry = (Map.Entry<String, String>) iteratorE.next();
		   	String key = entry.getKey();
		   	String value = entry.getValue();
		   	System.out.println("[key]:" + key + ", [value]:" + value);
		}
        
        System.out.println("------------------------------------------------------");
		
		// 방법 04 : keySet().iterator()
		Iterator<String> iteratorK = map.keySet().iterator();
		while (iteratorK.hasNext()) {
		String key = iteratorK.next();
		String value = map.get(key);
		System.out.println("[key]:" + key + ", [val]:" + value);
	}
        
결과
------------------------------------------------------
[key]:key1, [val]:val1
[key]:key2, [val]:val2
[key]:key5, [val]:val5
[key]:key3, [val]:val3
[key]:key4, [val]:val4
------------------------------------------------------
[key]:key1, [val]:val1
[key]:key2, [val]:val2
[key]:key5, [val]:val5
[key]:key3, [val]:val3
[key]:key4, [val]:val4
```

그리고 Iterator 인터페이스에서 자주 사용되는 메서드인 hasNext , next의 차이를 잠깐 알아봤다.

먼저, hasNext() 메서드는 boolean 타입으로 반환되고, next() 메서드는 매개변수 또는 iterator 되는 타입 (즉, anything~ 아무타입! ex) Iterator에 입력된 값들이 String이면 String로 반환) 으로 반환된다고 한다.
