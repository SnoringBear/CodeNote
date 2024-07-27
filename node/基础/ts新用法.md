# ts新用法



### 1、静态接口约束

```ts
export function StaticImplement<T>(): <U extends T>(constructor: U) => U;

```



这个函数的作用是返回一个装饰器函数，这个装饰器函数接受一个类作为参数，然后返回这个类本身。这个装饰器函数可以用于类的静态实现，确保类的静态属性和方法符合某个接口或类型的定义。





### 2、

```ts
declare function StaticSystemAttribute<T extends Entity>(CLASS: new () => T): ClassDecorator;

```


这段 TypeScript 代码声明了一个名为 `StaticSystemAttribute` 的函数。这个函数接受一个泛型类型 `T`，它必须是 `Entity` 类型的子类型，并接受一个名为 `CLASS` 的类构造函数作为参数。函数的返回类型是 `ClassDecorator`，这是一个装饰器，专门用于类。

换句话说，这个函数的作用是为类添加一个静态系统属性。