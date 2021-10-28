# Toaster.vue
## A Toast and Confirmation Component for VuetifyJs

Toaster allows you to generate small popup (toast) messages that wil
appear in any corner, left middle, top center, right middle, bottom center or center of the screen. These messages can be displayed for a configurable amount of time, with or without a countdown and close button. They can also be left open until the user responds to them by clicking the close button.

Toast message exist in different colors for Information (green), Warning (yellow) and Error (red). The colors are configurable. By default, error toasts remain until the user closes them.

All toast messages (or only a configurable subset by type) are stored and can be retrieved by clicking the toast icon in the lower right corner of the screen, which appears if any toasts are available for redisplay.

Toaster also provides screen centered alert and confirmation dialogs with configurable title, message, button text and header color.

## Usage

Import Toaster as a component in a global vue template, such as the class that contains your ```<router-view ... />```, like this:

```html
<template>
  <div>
    <transition name="hub-router-transition" mode="out-in">
        <router-view :key="$route.fullPath"/>
    </transition>
    <toaster :options="toasterOptions" />
  </div>
</template>
```

Place ``` <Toaster /> ``` as above, with the optional ':options' attribute if you like. You will also need to import Toaster.vue and specify it as a Component.

Then anywhere you want to display a toast message, import Toaster and use it directly or create simple toast wrapper functions.

### Import and Declare Toaster (TypeScript Example)

```ts
import Toaster from '@/components/Toaster.vue'; // do not include in the template or add as a component!

@Components({
  component: {
    Toaster
  }
})
```

### Static toast examples

```ts
// green in bottom right corner, times out after 4 seconds
Toaster.info(text);

// yellow in top right corner, times out after 2 seconds
Toaster.warn(text, 2000, Toaster.TopRight);

// red in the center of the screen, does not timeout and shows a Close button
Toaster.error(text, Toaster.WaitForClose, Toaster.Center);
```

### Toaster toast paramers are:

```ts
  message: string = '(no message)'
  timeout: number = 4000
  location: number | undefined = undefined
  ```

### Confirmation Dialog

To display a confirmation dialog, import Toaster and use Toaster.confirm()

```ts
Toaster.confirm('Press yes or no',
                () => { do something },
                () => { do something });
```

### Toaster confirm paramaters and default values for all but Function are:
```ts
  message: string = 'Do you want to continue'
  yesCallback: Function
  noCallback: Function
  yesPrompt: string = 'Yes'
  noPrompt: string = 'No'
  title: string = 'Please confirm'
```

### Alert Dialog

To display an alrt dialog, import Toaster and use Toaster.alert()

```ts
Toaster.alert('Press Ok', 'Alert title');
```

### Toaster alert paramaters and default values are:

```ts
  message: string = 'Press OK to continue',
  title: string = 'Alert!'
  okPrompt: string = 'OK'
  ```

## Toaster Options

### To set alternate defaults for Toaster, you can use the optional options attribute.

```html
<toaster :options="toasterOptions" />
```

#### The example below shows a toasterOptions object returning the **default values**. Any of these default values can be modified like this as well as overridden at run-time.

```ts
private get toasterOptions() {
  return {
    headerColor: 'primary white--text',
    infoColor: 'green',
    warnColor: 'yellow darken-3',
    errorColor: 'red'
    location: Toaster.BottomRight,
    timeout: 4000,
    showCountdown: true,
    showCloseButton: true,
  }
}
```