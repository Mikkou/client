---
metaTitle: Switcher сomponent | Awes.io
meta:
  - name: description
    content: The &lt;AwSwitcher /&gt; component is used to render Switcher - UI Vue component for Awes.io.
title: Switcher
---
# Switcher

The `AwSwitcher` component is used to render Switcher.

::: tip Content
[[toc]]
:::

## Usage
Several examples how you can use `AwSwitcher`.

### Preview
<iframe
     src='https://codesandbox.io/embed/github/awes-io/client/tree/master/examples/basic-ui?autoresize=1&fontsize=14&hidenavigation=1&initialpath=%2Faw-switcher&module=%2Fpages%2Faw-switcher.vue&theme=dark&view=preview'
     style='width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;'
     title='basic-ui'
     allow='geolocation; microphone; camera; midi; vr; accelerometer; gyroscope; payment; ambient-light-sensor; encrypted-media; usb'
     sandbox='allow-modals allow-forms allow-popups allow-scripts allow-same-origin'
   ></iframe>

### Editor
<iframe
     src='https://codesandbox.io/embed/github/awes-io/client/tree/master/examples/basic-ui?autoresize=1&fontsize=14&hidenavigation=1&initialpath=%2Faw-switcher&module=%2Fpages%2Faw-switcher.vue&theme=dark&view=editor'
     style='width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;'
     title='basic-ui'
     allow='geolocation; microphone; camera; midi; vr; accelerometer; gyroscope; payment; ambient-light-sensor; encrypted-media; usb'
     sandbox='allow-modals allow-forms allow-popups allow-scripts allow-same-origin'
   ></iframe>

## API
Select your desired component from below and see the available props, slots, events and functions.

### Props
Below is a collection of Vue **props** for the `AwSwitcher` component.
<!-- @vuese:AwSwitcher:props:start -->

<!-- @vuese:AwSwitcher:props:end -->

### Slots
Below is a collection of Vue **slots** for the `AwSwitcher` component.
<!-- @vuese:AwSwitcher:slots:start -->
|Name|Description|Default Slot Content|
|---|---|---|
|default|-|-|
|label|-|-|
|error|-|-|

<!-- @vuese:AwSwitcher:slots:end -->

### Events
Below is a collection of Vue **events** for the `AwSwitcher` component.
<!-- @vuese:AwSwitcher:events:start -->
|Event Name|Description|Parameters|
|---|---|---|
|change|-|-|

<!-- @vuese:AwSwitcher:events:end -->

## Examples
Below is a collection of simple to complex examples.

### Basic usage without props
```vue
<template>
    <AwPage title="Switcher">
        <AwSwitcher />
    </AwPage>
</template>

<script>
import { AwPage, AwSwitcher } from '@awes-io/ui'

export default {
    name: 'Switcher',

    components: {
        AwPage,
        AwSwitcher
    }
}
</script>

```

