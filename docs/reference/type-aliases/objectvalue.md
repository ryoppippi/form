---
id: ObjectValue
title: ObjectValue
---

<!-- DO NOT EDIT: this page is autogenerated from the type comments -->

# Type Alias: ObjectValue\<TParent, T, TKey\>

```ts
type ObjectValue<TParent, T, TKey> = T[TKey] | Nullable<TParent["value"]>;
```

Defined in: [packages/form-core/src/util-types.ts:91](https://github.com/TanStack/form/blob/main/packages/form-core/src/util-types.ts#L91)

## Type Parameters

• **TParent** *extends* [`AnyDeepKeyAndValue`](../../interfaces/anydeepkeyandvalue.md)

• **T**

• **TKey** *extends* [`AllObjectKeys`](../allobjectkeys.md)\<`T`\>
