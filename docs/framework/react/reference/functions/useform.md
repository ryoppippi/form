---
id: useForm
title: useForm
---

<!-- DO NOT EDIT: this page is autogenerated from the type comments -->

# Function: useForm()

```ts
function useForm<TFormData, TOnMount, TOnChange, TOnChangeAsync, TOnBlur, TOnBlurAsync, TOnSubmit, TOnSubmitAsync, TOnServer, TSubmitMeta>(opts?): ReactFormExtendedApi<TFormData, TOnMount, TOnChange, TOnChangeAsync, TOnBlur, TOnBlurAsync, TOnSubmit, TOnSubmitAsync, TOnServer, TSubmitMeta>
```

Defined in: [packages/react-form/src/useForm.tsx:143](https://github.com/TanStack/form/blob/main/packages/react-form/src/useForm.tsx#L143)

A custom React Hook that returns an extended instance of the `FormApi` class.

This API encapsulates all the necessary functionalities related to the form. It allows you to manage form state, handle submissions, and interact with form fields

## Type Parameters

• **TFormData**

• **TOnMount** *extends* `undefined` \| `FormValidateOrFn`\<`TFormData`\>

• **TOnChange** *extends* `undefined` \| `FormValidateOrFn`\<`TFormData`\>

• **TOnChangeAsync** *extends* `undefined` \| `FormAsyncValidateOrFn`\<`TFormData`\>

• **TOnBlur** *extends* `undefined` \| `FormValidateOrFn`\<`TFormData`\>

• **TOnBlurAsync** *extends* `undefined` \| `FormAsyncValidateOrFn`\<`TFormData`\>

• **TOnSubmit** *extends* `undefined` \| `FormValidateOrFn`\<`TFormData`\>

• **TOnSubmitAsync** *extends* `undefined` \| `FormAsyncValidateOrFn`\<`TFormData`\>

• **TOnServer** *extends* `undefined` \| `FormAsyncValidateOrFn`\<`TFormData`\>

• **TSubmitMeta**

## Parameters

### opts?

`FormOptions`\<`TFormData`, `TOnMount`, `TOnChange`, `TOnChangeAsync`, `TOnBlur`, `TOnBlurAsync`, `TOnSubmit`, `TOnSubmitAsync`, `TOnServer`, `TSubmitMeta`\>

## Returns

[`ReactFormExtendedApi`](../../type-aliases/reactformextendedapi.md)\<`TFormData`, `TOnMount`, `TOnChange`, `TOnChangeAsync`, `TOnBlur`, `TOnBlurAsync`, `TOnSubmit`, `TOnSubmitAsync`, `TOnServer`, `TSubmitMeta`\>
