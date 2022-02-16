Step by step tutorial on how to create Typescript deep merge generic type which works with inconsistent key values structures.


## TLDR:
Source code for DeepMergeTwoTypes generic is at bottom of the article.
You can copy-paste it into your IDE and play with it.

[you can play with the code here](https://www.typescriptlang.org/play?target=1#code/LAKALgngDgpgBACRgQwCYB4AqA+OBeOTOGADzBgDtUBnOAbQEsKAzGAJzgEkAaOAOgFNWHAPoAlGNTABdOAH4ucAFxwKMAG7tQkWIWQMANllwEipclVqMW7OCJ79BNjhKmyFrsMtUatIbdDwAFoMUCIAIjAwUACy7ADmMJgA7gD2mIHUWLwAqiaExGSUNPTSoHDycDnl3jmFFiV0ZSAVCpg1KnQ1FRWR0XFsiSnpmehIaMa84xh52NzdPQJ8IWF9sQlJaRmwWZj6Rji8e4boszXNoKAA9ABUN+U3egDW8GBpcKkARgBWMADGYFoRGQVCqcBBqDgfzYKHIcDAAAt4Gpkh81HBkgxEXAAK4UBgARzgLwgtGYqQ4wLBX1+AM4DzgSIMsA4iTUbAYfzg5I4AAM1gMhltMryHlcAroAOIwMAAeR+4QYzGElDAAGkYKT0DVMPMWlU9RVMAAGfBwWUAWyx2WJmtSzCquAAZOarWBTrwSfbCHMdQBGM0Ab3oargTFtEG9JukKhNdDVsgAvuV8pg-dc7gy9i94e8af9AQUIWDi9DYa8kT5Ual0ZjsYj4NRkBb4PmAbQSdQGUyWXA2exOdyKXB+VF1oNNiMdqKWjdxeBAnBpXKfgBlZswDVa3WOs2W61EAA+Boj3uX8u+iuV7FVW92uWw2EuC90gsnF4LWR1hpPNSuV1UGs4GoBFkBhSFO3BGEPigMAGBrZADB1U0CAABTAuDEPQc8FSVFUKHVTV70dJ99X-YDQPAiNaDA+AYT+HE2GoBhNAMCA4BhahUgMTRIU+djR36DZhm2SR0CWbAZwqF1A3jMMKFPB0cO+dcWzvG08hjOABWE4UdiweNpFyQzsETH80yDEN5MUwhjS0uMEzgZMQFTdN-BAcisTgFsJzgAAmYCwGQOCuR0SRwVBMA2HYsBUnBdRUgYSFvTxNQ-kkJtopguCa1oAAKXkAHJeQASlAUgoApLwwu0sc3xE0YdzyfA-wAgBaDqOv88FUFQGBIQMJhws6zqajoJrZHMYorBBCAml4WammaHpKhWCI6t0qcxKa0iVvIv4kT+J4wwdfsOVCzIoNbH5PwWToJvqab6GDOgSRUKQOQoeItLxJ4KFSZIKDMuAXrewLPu+lRfv+wGnLgZaVsqeq0g-dsNN2xHYzgY9qnc652pGwm2vhSQvD+ZBqGGonqefGq31QVHCwIZHUkZrJAxqclUk6BZgxIFRCuQQreAgAXPkKszzhqT4wJUDn9QqGW2BEZABaFn9FbAkRPjF4WFiVkQ-jlhYKj+I2QahM25bgEhDbN83CpIYXLatuBCrts3Crh5yVp9noDdQY2FZ6XqBd6wqFkl-Uo81gAvTp5ZWnWQb9xNmijxOh25+hefBNXneTwrxZjnpg1Ft31Zt-m3eLjWLYdyvC9rqX9SVoOk611WQZNy2VAoHELU+dg64qQO3dQCPg5LzXleT-uDAMOuDfN4Me9d4N7et+3XcKgAWEg-i9xM4cjmoS5l+Pnv19vEa7wXJ99s-09I58JXgAANM16bZoA)

[Or check the GitHub repo https://github.com/Svehla/TS_DeepMerge](https://github.com/Svehla/TS_DeepMerge)

```typescript
type A = { key1: { a: { b: 'c' } }, key2: undefined }
type B = { key1: { a: {} }, key3: string }
type MergedAB = DeepMergeTwoTypes<A, B>
```

![deep merge preview](https://raw.githubusercontent.com/Svehla/TS_DeepMerge/master/imgs/deep_merge_preview.png)

## Prerequisite
If you want to deep dive into advanced typescript types I recommend this typescript series full of useful examples.

- Basic static types inferring: https://dev.to/svehla/typescript-inferring-stop-writing-tests-avoid-runtime-errors-pt1-33h7

- More advanced generics https://dev.to/svehla/typescript-generics-stop-writing-tests-avoid-runtime-errors-pt2-2k62


## Typescript `&` operator behavior problem
First of all, we’ll look at the problem with the Typescript type merging. Let’s define two types `A` and `B` and a new type `MergedAB` which is the result of the merge `A & B`.

```typescript
type A = { key1: string, key2: string }
type B = { key1: string, key3: string }

type MergedAB = (A & B)['key1']
```

![Ts native merge](https://raw.githubusercontent.com/Svehla/TS_DeepMerge/master/imgs/ts_native_merge.png)

Everything looks good until you start to merge inconsistent data types. 

```typescript
type A = { key1: string, key2: string }
type B = { key2: null, key3: string }

type MergedAB = (A & B)
```

As you can see type `A` define `key2` as a string but type `B` define `key2` as a `null` value.

![Ts native merge 2](https://raw.githubusercontent.com/Svehla/TS_DeepMerge/master/imgs/ts_native_merge_2.png)


Typescript resolves this inconsistent type merging as type `never` and type `MergedAB` stops to work at all. Our expected output should be something like this

```typescript
type ExpectedType = {
  key1: string | null,
  key2: string,
  key3: string
}
```


## Step-by-step Solution

Let’s created a proper generic that recursively deep merge Typescript types.

First of all, we define 2 helper generic types.

### `GetObjDifferentKeys<>`

```typescript
type GetObjDifferentKeys<
  T,
  U,
  T0 = Omit<T, keyof U> & Omit<U, keyof T>,
  T1 = {
    [K in keyof T0]: T0[K]
  }
 > = T1
```

this type takes 2 Objects and returns a new object contains only unique keys in `A` and `B`. 

```typescript
type A = { key1: string, key2: string }
type B = { key2: null, key3: string }

type DifferentKeysAB = (GetObjDifferentKeys<A, B>)['k']
```

![different keys](https://raw.githubusercontent.com/Svehla/TS_DeepMerge/master/imgs/different_keys_ab.png)



### `GetObjSameKeys<>`
For the opposite of the previous generic, we will define a new one that picks all keys which are the same in both objects.

```typescript
type GetObjSameKeys<T, U> = Omit<T | U, keyof GetObjDifferentKeys<T, U>>
```


The returned type is an object.

```typescript
type A = { key1: string, key2: string }
type B = { key2: null, key3: string }
type SameKeys = GetObjSameKeys<A, B>
```

![Same keys](https://raw.githubusercontent.com/Svehla/TS_DeepMerge/master/imgs/same_keys.png)



All helpers functions are Done so we can start to implement the main **`DeepMergeTwoTypes`** generic.

## `DeepMergeTwoTypes<>`

```typescript
type DeepMergeTwoTypes<
  T,
  U, 
  // non shared keys are optional
  T0 = Partial<GetObjDifferentKeys<T, U>>
    // shared keys are required
    & { [K in keyof GetObjSameKeys<T, U>]: T[K] | U[K] },
  T1 = { [K in keyof T0]: T0[K] }
> = T1
 
```



This generic finds all nonshared keys between object `T` and `U` and makes them optional thanks to `Partial<>` generic provided by Typescript. This type with Optional keys is merged via `&` an operator with the object that contains all `T` and `U` shared keys which values are of type `T[K] | U[K]`.

As you can see in the example below. New generic found non-shared keys and make them optional `?` the rest of keys is strictly required.


```typescript
type A = { key1: string, key2: string }
type B = { key2: null, key3: string }
type MergedAB = DeepMergeTwoTypes<A, B>
```

![deep merge two types](https://raw.githubusercontent.com/Svehla/TS_DeepMerge/master/imgs/deep_merge_two_types.png)


But our current `DeepMergeTwoTypes` generic does not work recursively to the nested structures types. So let’s extract Object merging functionality into a new generic called `MergeTwoObjects` and let `DeepMergeTwoTypes` call recursively until it merges all nested structures.

```typescript
// this generic call recursively DeepMergeTwoTypes<>

type MergeTwoObjects<
  T,
  U, 
  // non shared keys are optional
  T0 = Partial<GetObjDifferentKeys<T, U>>
  // shared keys are recursively resolved by `DeepMergeTwoTypes<...>`
  & {[K in keyof GetObjSameKeys<T, U>]: DeepMergeTwoTypes<T[K], U[K]>},
  T1 = { [K in keyof T0]: T0[K] }
> = T1

export type DeepMergeTwoTypes<T, U> =
  // check if generic types are arrays and unwrap it and do the recursion
  [T, U] extends [{ [key: string]: unknown}, { [key: string]: unknown } ]
    ? MergeTwoObjects<T, U>
    : T | U
```



*PRO TIP: You can see that in the DeepMergeTwoTypes an if-else condition we merged type `T` and `U` into tuple `[T, U]` for verifying that both types passed successfully the condition (similarly as the `&&` operator in the javascript conditions)*

This generic checks that both parameters are of type `{ [key: string]: unknown }` (aka `Object`). If it’s true it merges them via `MergeTwoObject<>`. This process is recursively repeated for all nested objects.


And voilá 🎉 now the generic is recursively applied on all nested objects
example:

```typescript
type A = { key: { a: null, c: string} }
type B = { key: { a: string, b: string} }

type MergedAB = DeepMergeTwoTypes<A, B>
```

![deep merge two types 2](https://raw.githubusercontent.com/Svehla/TS_DeepMerge/master/imgs/deep_merge_two_types_2.png)

*Is that all?*

Unfortunately not… Our new generic does not support Arrays.

## Add arrays support

Before we will continue we have to know the keyword `infer`.

`infer` look for data structure and extract data type which is wrapped inside of them (in our case it extract data type of array) You can read more about `infer` functionality there:
https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-8.html#type-inference-in-conditional-types

### Let's define another helper generics!


#### `Head<T>`

`Head` This generic takes an array and returns the first item.

```typescript
type Head<T> = T extends [infer I, ...infer _Rest] ? I : never

type T0 = Head<['x', 'y', 'z']>
```

![Head array](https://raw.githubusercontent.com/Svehla/TS_DeepMerge/master/imgs/head_array.png)

#### `Tail<T>`
This generic takes an array and returns all items exclude the first one.

```typescript
type Tail<T> = T extends [infer _I, ...infer Rest] ? Rest : never

type T0 = Tail<['x', 'y', 'z']>
```

![tail array](https://raw.githubusercontent.com/Svehla/TS_DeepMerge/master/imgs/tail_array.png)

That is all we need for the final implementation of arrays merging Generic, so let's hack it!

### `Zip_DeepMergeTwoTypes<T, U>`

`Zip_DeepMergeTwoTypes` is a simple recursive generic which zip two arrays into one by combining their items based on the item index position.

```typescript
type Head<T> = T extends [infer I, ...infer _Rest] ? I : never
type Tail<T> = T extends [infer _I, ...infer Rest] ? Rest : never

type Zip_DeepMergeTwoTypes<T, U> = T extends []
  ? U
  : U extends []
  ? T
  : [
      DeepMergeTwoTypes<Head<T>, Head<U>>,
      ...Zip_DeepMergeTwoTypes<Tail<T>, Tail<U>>
  ]

type T0 = Zip_DeepMergeTwoTypes<
  [
    { a: 'a', b: 'b'},
  ],
  [
    { a: 'aaaa', b: 'a', c: 'b'},
    { d: 'd', e: 'e', f: 'f' }
  ]
>

```

![zip deep merge two types](https://raw.githubusercontent.com/Svehla/TS_DeepMerge/master/imgs/zip_deep_merge_two_types.png)

Now we'll just write 2 lines long integration in the `DeepMergeTwoTypes<T, U>` Generic which provides zipping values thanks to `Zip_DeepMergeTwoTypes` Generic.

```typescript
export type DeepMergeTwoTypes<T, U> =
  // ----- 2 added lines ------
  // this line ⏬
  [T, U] extends [any[], any[]]
    // ... and this line ⏬
    ? Zip_DeepMergeTwoTypes<T, U>
    // check if generic types are objects
    : [T, U] extends [{ [key: string]: unknown}, { [key: string]: unknown } ]
      ? MergeTwoObjects<T, U>
      : T | U
```
--------------------------------------------------

## And…. That’s all!!! 🎉

We did it! Values are correctly merged even for nullable values, nested objects, and long arrays.

Let’s try it on some more complex data

```typescript
type A = { key1: { a: { b: 'c'} }, key2: undefined }
type B = { key1: { a: {} }, key3: string }

          
type MergedAB = DeepMergeTwoTypes<A, B>
```
![deep merge preview](https://raw.githubusercontent.com/Svehla/TS_DeepMerge/master/imgs/deep_merge_preview.png)




## Full source code

```typescript
type Head<T> = T extends [infer I, ...infer _Rest] ? I : never
type Tail<T> = T extends [infer _I, ...infer Rest] ? Rest : never

type Zip_DeepMergeTwoTypes<T, U> = T extends []
  ? U
  : U extends []
  ? T
  : [
      DeepMergeTwoTypes<Head<T>, Head<U>>,
      ...Zip_DeepMergeTwoTypes<Tail<T>, Tail<U>>
  ]


/**
 * Take two objects T and U and create the new one with uniq keys for T a U objectI
 * helper generic for `DeepMergeTwoTypes`
 */
type GetObjDifferentKeys<
  T,
  U,
  T0 = Omit<T, keyof U> & Omit<U, keyof T>,
  T1 = { [K in keyof T0]: T0[K] }
 > = T1
/**
 * Take two objects T and U and create the new one with the same objects keys
 * helper generic for `DeepMergeTwoTypes`
 */
type GetObjSameKeys<T, U> = Omit<T | U, keyof GetObjDifferentKeys<T, U>>

type MergeTwoObjects<
  T,
  U, 
  // non shared keys are optional
  T0 = Partial<GetObjDifferentKeys<T, U>>
  // shared keys are recursively resolved by `DeepMergeTwoTypes<...>`
  & {[K in keyof GetObjSameKeys<T, U>]: DeepMergeTwoTypes<T[K], U[K]>},
  T1 = { [K in keyof T0]: T0[K] }
> = T1

// it merge 2 static types and try to avoid of unnecessary options (`'`)
export type DeepMergeTwoTypes<T, U> =
  // ----- 2 added lines ------
  [T, U] extends [any[], any[]]
    ? Zip_DeepMergeTwoTypes<T, U>
    // check if generic types are objects
    : [T, U] extends [{ [key: string]: unknown}, { [key: string]: unknown } ]
      ? MergeTwoObjects<T, U>
      : T | U

```

[you can play with the code here](https://www.typescriptlang.org/play?target=1#code/LAKALgngDgpgBACRgQwCYB4AqA+OBeOTOGADzBgDtUBnOAbQEsKAzGAJzgEkAaOAOgFNWHAPoAlGNTABdOAH4ucAFxwKMAG7tQkWIWQMANllwEipclVqMW7OCJ79BNjhKmyFrsMtUatIbdDwAFoMUCIAIjAwUACy7ADmMJgA7gD2mIHUWLwAqiaExGSUNPTSoHDycDnl3jmFFiV0ZSAVCpg1KnQ1FRWR0XFsiSnpmehIaMa84xh52NzdPQJ8IWF9sQlJaRmwWZj6Rji8e4boszXNoKAA9ABUN+U3egDW8GBpcKkARgBWMADGYFoRGQVCqcBBqDgfzYKHIcDAAAt4Gpkh81HBkgxEXAAK4UBgARzgLwgtGYqQ4wLBX1+AM4DzgSIMsA4iTUbAYfzg5I4AAM1gMhltMryHlcAroAOIwMAAeR+4QYzGElDAAGkYKT0DVMPMWlU9RVMAAGfBwWUAWyx2WJmtSzCquAAZOarWBTrwSfbCHMdQBGM0Ab3oargTFtEG9JukKhNdDVsgAvuV8pg-dc7gy9i94e8af9AQUIWDi9DYa8kT5Ual0ZjsYj4NRkBb4PmAbQSdQGUyWXA2exOdyKXB+VF1oNNiMdqKWjdxeBAnBpXKfgBlZswDVa3WOs2W61EAA+Boj3uX8u+iuV7FVW92uWw2EuC90gsnF4LWR1hpPNSuV1UGs4GoBFkBhSFO3BGEPigMAGBrZADB1U0CAABTAuDEPQc8FSVFUKHVTV70dJ99X-YDQPAiNaDA+AYT+HE2GoBhNAMCA4BhahUgMTRIU+djR36DZhm2SR0CWbAZwqF1A3jMMKFPB0cO+dcWzvG08hjOABWE4UdiweNpFyQzsETH80yDEN5MUwhjS0uMEzgZMQFTdN-BAcisTgFsJzgAAmYCwGQOCuR0SRwVBMA2HYsBUnBdRUgYSFvTxNQ-kkJtopguCa1oAAKXkAHJeQASlAUgoApLwwu0sc3xE0YdzyfA-wAgBaDqOv88FUFQGBIQMJhws6zqajoJrZHMYorBBCAml4WammaHpKhWCI6t0qcxKa0iVvIv4kT+J4wwdfsOVCzIoNbH5PwWToJvqab6GDOgSRUKQOQoeItLxJ4KFSZIKDMuAXrewLPu+lRfv+wGnLgZaVsqeq0g-dsNN2xHYzgY9qnc652pGwm2vhSQvD+ZBqGGonqefGq31QVHCwIZHUkZrJAxqclUk6BZgxIFRCuQQreAgAXPkKszzhqT4wJUDn9QqGW2BEZABaFn9FbAkRPjF4WFiVkQ-jlhYKj+I2QahM25bgEhDbN83CpIYXLatuBCrts3Crh5yVp9noDdQY2FZ6XqBd6wqFkl-Uo81gAvTp5ZWnWQb9xNmijxOh25+hefBNXneTwrxZjnpg1Ft31Zt-m3eLjWLYdyvC9rqX9SVoOk611WQZNy2VAoHELU+dg64qQO3dQCPg5LzXleT-uDAMOuDfN4Me9d4N7et+3XcKgAWEg-i9xM4cjmoS5l+Pnv19vEa7wXJ99s-09I58JXgAANM16bZoA)

[Or check the GitHub repo https://github.com/Svehla/TS_DeepMerge](https://github.com/Svehla/TS_DeepMerge)




## And what's next?

If you're interested in another advanced usage of the Typescript type system, you can check these step-by-step articles/tutorials on how to create some advanced Typescript generics.

- [World-first Static time RegEx engine with O(0) time complexity](https://dev.to/svehla/world-first-static-time-regex-engine-with-o-0-time-complexity-4k4e)
- [How to Object.fromEntries tuples](https://dev.to/svehla/typescript-object-fromentries-389c)
- [UPPER_CASE to lowerCase transformator](https://dev.to/svehla/typescript-transform-case-strings-450b)
- [and so on](https://dev.to/svehla)



🎉🎉🎉🎉🎉
