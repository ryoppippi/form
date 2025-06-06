---
id: form-validation
title: Form and Field Validation
---

At the core of TanStack Form's functionalities is the concept of validation. TanStack Form makes validation highly customizable:

- You can control when to perform the validation (on change, on input, on blur, on submit...)
- Validation rules can be defined at the field level or at the form level
- Validation can be synchronous or asynchronous (for example, as a result of an API call)

## When is validation performed?

It's up to you! The `<form.Field />` component accepts some callbacks as props such as `onChange` or `onBlur`. Those callbacks are passed the current value of the field, as well as the fieldAPI object, so that you can perform the validation. If you find a validation error, simply return the error message as string and it will be available in `field.state.meta.errors`.

Here is an example:

```svelte
<form.Field
  name="age"
  validators={{
    onChange: ({ value }) =>
      value < 13 ? 'You must be 13 to make an account' : undefined,
  }}
>
  {#snippet children(field)}
    <label for={field.name}>Age:</label>
    <input
      id={field.name}
      name={field.name}
      value={field.state.value}
      type="number"
      onchange={(e) => field.handleChange(e.target.valueAsNumber)}
    />
    {#if field.state.meta.errors}
      <em role="alert">{field.state.meta.errors.join(', ')}</em>
    {/if}
  {/snippet}
</form.Field>
```

In the example above, the validation is done at each keystroke (`onchange`). If, instead, we wanted the validation to be done when the field is blurred, we would change the code above like so:

```svelte
<form.Field
  name="age"
  validators={{
    onBlur: ({ value }) =>
      value < 13 ? 'You must be 13 to make an account' : undefined,
  }}
>
  {#snippet children(field)}
    <label for={field.name}>Age:</label>
    <input
      id={field.name}
      name={field.name}
      value={field.state.value}
      type="number"
      onblur={field.handleBlur}
      onchange={(e) => field.handleChange(e.target.valueAsNumber)}
    />
    {#if field.state.meta.errors}
      <em role="alert">{field.state.meta.errors.join(', ')}</em>
    {/if}
  {/snippet}
</form.Field>
```

So you can control when the validation is done by implementing the desired callback. You can even perform different pieces of validation at different times:

```svelte
<form.Field
  name="age"
  validators={{
    onChange: ({ value }) =>
      value < 13 ? 'You must be 13 to make an account' : undefined,
    onBlur: ({ value }) => (value < 0 ? 'Invalid value' : undefined),
  }}
>
  {#snippet children(field)}
    <label for={field.name}>Age:</label>
    <input
      id={field.name}
      name={field.name}
      value={field.state.value}
      type="number"
      onblur={field.handleBlur}
      onchange={(e) => field.handleChange(e.target.valueAsNumber)}
    />
    {#if field.state.meta.errors}
      <em role="alert">{field.state.meta.errors.join(', ')}</em>
    {/if}
  {/snippet}
</form.Field>
```

In the example above, we are validating different things on the same field at different times (at each keystroke and when blurring the field). Since `field.state.meta.errors` is an array, all the relevant errors at a given time are displayed. You can also use `field.state.meta.errorMap` to get errors based on _when_ the validation was done (onChange, onBlur etc...). More info about displaying errors below.

## Displaying Errors

Once you have your validation in place, you can map the errors from an array to be displayed in your UI:

```svelte
<form.Field
  name="age"
  validators={{
    onChange: ({ value }) =>
      value < 13 ? 'You must be 13 to make an account' : undefined,
  }}
>
  {#snippet children(field)}
    <!-- ... -->
    {#if field.state.meta.errors}
      <em role="alert">{field.state.meta.errors.join(', ')}</em>
    {/if}
  {/snippet}
</form.Field>
```

Or use the `errorMap` property to access the specific error you're looking for:

```svelte
<form.Field
  name="age"
  validators={{
    onChange: ({ value }) =>
      value < 13 ? 'You must be 13 to make an account' : undefined,
  }}
>
  {#snippet children(field)}
    <!-- ... -->
    {#if field.state.meta.errorMap['onChange']}
      <em role="alert">{field.state.meta.errorMap['onChange']}</em>
    {/if}
  {/snippet}
</form.Field>
```

It's worth mentioning that our `errors` array and the `errorMap` matches the types returned by the validators. This means that:

```svelte
<form.Field
  name="age"
  validators={{
    onChange: ({ value }) => (value < 13 ? { isOldEnough: false } : undefined),
  }}
>
  {#snippet children(field)}
    <!-- ... -->
    <!-- errorMap.onChange is type `{isOldEnough: false} | undefined` -->
    <!-- meta.errors is type `Array<{isOldEnough: false} | undefined>` -->
    {#if field.state.meta.errorMap['onChange']?.isOldEnough}
        <em>The user is not old enough</em>
    {/if}
  {/snippet}
</form.Field>
```

## Validation at field level vs at form level

As shown above, each `<form.Field>` accepts its own validation rules via the `onChange`, `onBlur` etc... callbacks. It is also possible to define validation rules at the form level (as opposed to field by field) by passing similar callbacks to the `createForm()` hook.

Example:

```svelte
<script>
  import { createForm } from '@tanstack/svelte-form'

  const form = createForm(() => ({
    defaultValues: {
      age: 0,
    },
    onSubmit: async ({ value }) => {
      console.log(value)
    },
    validators: {
      // Add validators to the form the same way you would add them to a field
      onChange({ value }) {
        if (value.age < 13) {
          return 'Must be 13 or older to sign'
        }
        return undefined
      },
    },
  }))

  // Subscribe to the form's error map so that updates to it will render
  // alternately, you can use `form.Subscribe`
  const formErrorMap = form.useStore((state) => state.errorMap)
</script>

<div>
  <!-- ... -->
  {#if formErrorMap.current.onChange}
    <div>
      <em>There was an error on the form: {formErrorMap.current.onChange}</em>
    </div>
  {/if}
  <!-- ... -->
</div>
```

## Asynchronous Functional Validation

While we suspect most validations will be synchronous, there are many instances where a network call or some other async operation would be useful to validate against.

To do this, we have dedicated `onChangeAsync`, `onBlurAsync`, and other methods that can be used to validate against:

```svelte
<form.Field
  name="age"
  validators={{
    onChangeAsync: async ({ value }) => {
      await new Promise((resolve) => setTimeout(resolve, 1000))
      return value < 13 ? 'You must be 13 to make an account' : undefined
    },
  }}
>
  {#snippet children(field)}
    <label for={field.name}>Age:</label>
    <input
      id={field.name}
      name={field.name}
      value={field.state.value}
      type="number"
      onchange={(e) => field.handleChange(e.target.valueAsNumber)}
    />
    {#if field.state.meta.errors}
      <em role="alert">{field.state.meta.errors.join(', ')}</em>
    {/if}
  {/snippet}
</form.Field>
```

Synchronous and Asynchronous validations can coexist. For example, it is possible to define both `onBlur` and `onBlurAsync` on the same field:

```svelte
<form.Field
  name="age"
  validators={{
    onBlur: ({ value }) => (value < 13 ? 'You must be at least 13' : undefined),
    onBlurAsync: async ({ value }) => {
      const currentAge = await fetchCurrentAgeOnProfile()
      return value < currentAge ? 'You can only increase the age' : undefined
    },
  }}
>
  {#snippet children(field)}
    <label for={field.name}>Age:</label>
    <input
      id={field.name}
      name={field.name}
      value={field.state.value}
      type="number"
      onblur={field.handleBlur}
      onchange={(e) => field.handleChange(e.target.valueAsNumber)}
    />
    {#if field.state.meta.errors}
      <em role="alert">{field.state.meta.errors.join(', ')}</em>
    {/if}
  {/snippet}
</form.Field>
```

The synchronous validation method (`onBlur`) is run first and the asynchronous method (`onBlurAsync`) is only run if the synchronous one (`onBlur`) succeeds. To change this behaviour, set the `asyncAlways` option to `true`, and the async method will be run regardless of the result of the sync method.

### Built-in Debouncing

While async calls are the way to go when validating against the database, running a network request on every keystroke is a good way to DDOS your database.

Instead, we enable an easy method for debouncing your `async` calls by adding a single property:

```svelte
<form.Field
  name="age"
  asyncDebounceMs={500}
  validators={{
    onChangeAsync: async ({ value }) => {
      // ...
    },
  }}
>
  <!-- ... -->
</form.Field>
```

This will debounce every async call with a 500ms delay. You can even override this property on a per-validation property:

```svelte
<form.Field
  name="age"
  asyncDebounceMs={500}
  validators={{
    onChangeAsyncDebounceMs: 1500,
    onChangeAsync: async ({ value }) => {
      // ...
    },
    onBlurAsync: async ({ value }) => {
      // ...
    },
  }}
>
  <!-- ... -->
</form.Field>
```

> This will run `onChangeAsync` every 1500ms while `onBlurAsync` will run every 500ms.

## Validation through Schema Libraries

While functions provide more flexibility and customization over your validation, they can be a bit verbose. To help solve this, there are libraries that provide schema-based validation to make shorthand and type-strict validation substantially easier. You can also define a single schema for your entire form and pass it to the form level, errors will be automatically propagated to the fields.

### Standard Schema Libraries

TanStack Form natively supports all libraries following the [Standard Schema specification](https://github.com/standard-schema/standard-schema), most notably:

- [Zod](https://zod.dev/)
- [Valibot](https://valibot.dev/)
- [ArkType](https://arktype.io/)

_Note:_ make sure to use the latest version of the schema libraries as older versions might not support Standard Schema yet.

To use schemas from these libraries you can pass them to the `validators` props as you would do with a custom function:

```svelte
<script>
  import { z } from 'zod'

  // ...

  const form = createForm(() => ({
    // ...
  }))
</script>

<form.Field
  name="age"
  validators={{
    onChange: z.number().gte(13, 'You must be 13 to make an account'),
  }}
>
  <!-- ... -->
</form.Field>
```

Async validations on form and field level are supported as well:

```svelte
<form.Field
  name="age"
  validators={{
    onChange: z.number().gte(13, 'You must be 13 to make an account'),
    onChangeAsyncDebounceMs: 500,
    onChangeAsync: z.number().refine(
      async (value) => {
        const currentAge = await fetchCurrentAgeOnProfile()
        return value >= currentAge
      },
      {
        message: 'You can only increase the age',
      },
    ),
  }}
>
  <!-- ... -->
</form.Field>
```

## Preventing invalid forms from being submitted

The `onChange`, `onBlur` etc... callbacks are also run when the form is submitted and the submission is blocked if the form is invalid.

The form state object has a `canSubmit` flag that is false when any field is invalid and the form has been touched (`canSubmit` is true until the form has been touched, even if some fields are "technically" invalid based on their `onChange`/`onBlur` props).

You can subscribe to it via `form.Subscribe` and use the value in order to, for example, disable the submit button when the form is invalid (in practice, disabled buttons are not accessible, use `aria-disabled` instead).

```svelte
<script>
  import { createForm } from '@tanstack/svelte-form'

  const form = createForm(() => ({
    /* ... */
  }))
</script>

<!-- ... -->

<!-- Dynamic submit button -->
<form.Subscribe
  selector={(state) => ({
    canSubmit: state.canSubmit,
    isSubmitting: state.isSubmitting,
  })}
  children={(state) => (
    <button type="submit" disabled={!state().canSubmit}>
      {state().isSubmitting ? '...' : 'Submit'}
    </button>
  )}
>
  {#snippet children(state)}
    <button type="submit" disabled={!state.canSubmit}>
      {state.isSubmitting ? '...' : 'Submit'}
    </button>
  {/snippet}
</form.Subscribe>
```
