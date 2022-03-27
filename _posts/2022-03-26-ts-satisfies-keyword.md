---
layout: post
toc: false
math: false
title: "Typescript satisfies keyword"
categories: ["Programming"]
tags: [typescript, javascript]
author:
  - 이현재
---

With Typescript, you can do strict type checking at compile time or before,
thanks to Typescript's features and help from IDE.
<!--more-->

Let us say we want to define an object having
properties of smartphones.
We can achieve it like the code below:

```ts
const SMARTPHONE_MAPPING = {
  IPHONE_12 : {displaySize:   6.06,  weight: "164g"},
  GALAXY_S22: {displaySize:   6.1,   weight: "168g"},
};
```

Of course, there are numerous phones out there,
so we might add more phones later on. Also,
we do not know what names those phone will be called,
so `SMARTPHONE_MAPPING` would have arbitrary random keys.

But while adding more phones, we do not want to introduce any errors,
such as usual ***developer errors*** like below:

```ts
const SMARTPHONE_MAPPING = {
  IPHONE_12 : {displaySize:   6.06,  weight: "164g"},
  GALAXY_S22: {displaySize:   6.1,   weight: "168g"},
  IPHONE_13 : {dispaylSize:   7,     weight: "150g"}, // key typo
  GALAXY_S23: {displaySize:   7,                   }, // missing key
};
```
<br>

What we want inside `SMARTPHONE_MAPPING` are nothing but
display size and weight properties only.
How do we ensure that the values in the object are of the type?
Well, we can give a type to the object, like below:

```ts
type SMARTPHONE_MAP_TYPE = {[key: string]: {displaySize: number, weight: string}}
// Or we can defind the type as Record.
// type SMARTPHONE_MAP_TYPE = Record<string, {displaySize: number, weight: string}}>

const SMARTPHONE_MAPPING: SMARTPHONE_MAP_TYPE = {
  IPHONE_12 : {displaySize:   6.06,  weight: "164g"},
  GALAXY_S22: {displaySize:   6.1,   weight: "168g"},
};
```
<br>

But the problem of giving the object a type is that
IDE now does not show us the autocomplete of the member keys.

![with-autocomplete](/img/2022-03-26-ts-satisfies-keyword/with-autocomplete.png)

**Before Having explicit type**
<br><br>

![without-autocomplete](/img/2022-03-26-ts-satisfies-keyword/without-autocomplete.png)

**After Having explicit type**
<br><br>

Well, why does this happen?
This is because the type of the variable is `SMARTPHONE_MAP_TYPE`,
where there is no information about what is in the object.
**All the information about what's inside the variable
has been deprecated.** All it knows about the keys is that
they are string type. Please take a look at the type hints
and find out what are the differences.

![without-type](/img/2022-03-26-ts-satisfies-keyword/without-type.png)

**Before Having explicit type**
<br><br>

![with-type](/img/2022-03-26-ts-satisfies-keyword/with-type.png)

**After Having explicit type**
<br><br>

`SMARTPHONE_MAP_TYPE` does not contain any information about
what are members but only that the keys are in string,
and the values are in object containing the two properties.

`SMARTPHONE_MAPPING` does know the member keys, but
giving the explicit type to the object made it
blind about what is inside the object.

## Solution
To solve this, we can implement a generic function
which accepts `arg` which extends `SMARTPHONE_MAP_TYPE`,
and returns `arg` as is. See `asType` function below:

```ts
type SMARTPHONE_MAP_TYPE = {[key: string]: {displaySize: number, weight: string}}

function asType<T extends SMARTPHONE_MAP_TYPE>(arg: T): T{
  return arg;
}

const SMARTPHONE_MAPPING = asType({
  IPHONE_12 : {displaySize:   6.06,  weight: "164g"},
  GALAXY_S22: {displaySize:   6.1,   weight: "168g"},
});
```

Since the function returns `T` type which extends `SMARTPHONE_MAP_TYPE`,
we can have strict type checking.
And since the function returns `arg` which is of `T` type,
not `SMARTPHONE_MAP_TYPE`, the returned value has information what is inside itself.

![strict-type-checking](/img/2022-03-26-ts-satisfies-keyword/strict-type-checking.png)

![autocomplete](/img/2022-03-26-ts-satisfies-keyword/autocomplete.png)

Now we have both strict type checking and autocompletion. :)

However, this solution is a little bit tedious.
Luckily, Typescript is going to introduce `satisfies` keyword
in the near future, maybe in `4.7` version.

```ts
type SMARTPHONE_MAP_TYPE = {[key: string]: {displaySize: number, weight: string}}

const SMARTPHONE_MAPPING = {
  IPHONE_12 : {displaySize:   6.06,  weight: "164g"},
  GALAXY_S22: {displaySize:   6.1,   weight: "168g"},
} satisfies SMARTPHONE_MAP_TYPE;
```

Check out the related Github issue on Typescript repository for more!
[[Link]](https://github.com/microsoft/TypeScript/issues/47920)
