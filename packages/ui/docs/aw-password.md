---
metaTitle: Password сomponent | Awes.io
meta:
  - name: description
    content: The &lt;AwPassword /&gt; component is used to render Password - UI Vue component for Awes.io.
title: Password
---
# Password

The `AwPassword` component is used to render Password.

::: tip Content
[[toc]]
:::

## Usage
Several examples how you can use `AwPassword`.

### Preview
<iframe
     src='https://codesandbox.io/embed/github/awes-io/client/tree/master/examples/basic-ui?autoresize=1&fontsize=14&hidenavigation=1&initialpath=%2Faw-input&module=%2Fpages%2Faw-input.vue&theme=dark&view=preview'
     style='width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;'
     title='basic-ui'
     allow='geolocation; microphone; camera; midi; vr; accelerometer; gyroscope; payment; ambient-light-sensor; encrypted-media; usb'
     sandbox='allow-modals allow-forms allow-popups allow-scripts allow-same-origin'
   ></iframe>

### Editor
<iframe
     src='https://codesandbox.io/embed/github/awes-io/client/tree/master/examples/basic-ui?autoresize=1&fontsize=14&hidenavigation=1&initialpath=%2Faw-input&module=%2Fpages%2Faw-input.vue&theme=dark&view=editor'
     style='width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;'
     title='basic-ui'
     allow='geolocation; microphone; camera; midi; vr; accelerometer; gyroscope; payment; ambient-light-sensor; encrypted-media; usb'
     sandbox='allow-modals allow-forms allow-popups allow-scripts allow-same-origin'
   ></iframe>

## API
Select your desired component from below and see the available props, slots, events and functions.

### Props
Below is a collection of Vue **props** for the `AwPassword` component.
<!-- @vuese:AwPassword:props:start -->

<!-- @vuese:AwPassword:props:end -->

### Slots
Below is a collection of Vue **slots** for the `AwPassword` component.
<!-- @vuese:AwPassword:slots:start -->

<!-- @vuese:AwPassword:slots:end -->

### Events
Below is a collection of Vue **events** for the `AwPassword` component.
<!-- @vuese:AwPassword:events:start -->

<!-- @vuese:AwPassword:events:end -->
## Examples
Below is a collection of simple to complex examples.

### Basic usage without props
```vue
<template>
    <AwPage title="Password">
        <AwPassword />
    </AwPage>
</template>

<script>
import { AwPage, AwPassword } from '@awes-io/ui'

export default {
    name: 'Password',

    components: {
        AwPage,
        AwPassword
    }
}
</script>

```

