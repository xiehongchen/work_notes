## 简单

### readonly

实现Readonly，接收一个泛型参数，并返回一个完全一样的类型，只是所有属性都会被readonly所修饰。

```typescript
type myReadonly<T> = {
  readonly [P in keyof T]: T[P]
}
interface Todo {
  title: string
  description: string
}
const todoObj: myReadonly<Todo> = {
  title: '字符串',
  description: '字符串',
}
console.log(todoObj)
todoObj.description = '修改'
```

### first-of-array

实现First，他接受一个数组 T 并返回它的第一个元素类型

```typescript
type First<T extends any[]> = T extends [] ? never : T[0];

type arr1 = ['a', 'b', 'c']
type arr2 = [3, 2, 1]

type head1 = First<arr1> // expected to be 'a'
type head2 = First<arr2> // expected to be 3
```

### tuple-to-object

实现TupleToObject，传入元组类型，将元组类型转换为对象类型，这个对象类型的键/值都是从元组中遍历出来。

```typescript
type TupleToObject<T extends readonly any[]> = {
  [P in T[number]]: P;
};

const tuple = ["tesla", "model 3", "model X", "model Y"] as const;

type result = TupleToObject<typeof tuple>; // expected { tesla: 'tesla', 'model 3': 'model 3', 'model X': 'model X', 'model Y': 'model Y'}
```

