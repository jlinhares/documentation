# ngm-orientation

Is a Angular module for using mobile device orientation sensor.

## Requirements

Angular 6.x.x

## Service

### - Adding module to application

In the application module, import `OrientationModule` from the package `ngm-orientation` and initialize it.

```typescript
import {OrientationModule} from 'ng-msensors';
@NgModule({
  imports: [
    ...
    OrientationModule.forRoot(),
    ...
  ],
  ...  
  bootstrap:    [ AppComponent ]
})
export class AppModule { }
```

However, it's possible to initialize the events on module import by adding a config parameter to the `forRoot` function.

```typescript
imports: [
    ...
    OrientationModule.forRoot({
        orientation: true,
        absolute_orientation: true
    }),
    ...
]
```

Or just one of them, like below

```typescript
imports: [
    ...
    OrientationModule.forRoot({
        orientation: true
    }),
    ...
]
```

This way, the `OrientationService` will be avalilable to application.

***Attention***

Device orientation don't work under iframe elements.

### - Using the service

On a component, directive or even other service, add the service (`OrientationService` and/or `AbsoluteOrientationService`  ) by dependency injection and code the event on the `OnInit` implementation.

```typescript
import { Directive, OnInit, OnDestroy } from '@angular/core';

import {Subscription} from 'rxjs';
import {OrientationService, OrientationEventData} from 'ngm-orientation';

@Directive({
  selector: '[yourDirective]'
})
export class YourDirective implements OnInit, OnDestroy {

  private orientation$: Subscription;
  
  constructor(private orsvc: OrientationService) {
    ...
  }

  ngOnInit() {
    this.orientation$ = this.orsvc.OrientationEvent.subscribe(
        (data: OrientationEventData) => {
            // event code
            ...
        }
    );
  }

  ngOnDestroy() {
    this.orientation$.unsubscribe();
  }
```

The `OrientationEventData` has the following structure

```typescript
{
    x: number,
    y: number,
    z: number,
    absolute: boolean
}
```

where x, y, z is numbers that represents the rotation in degrees around those axis and absolute is a boolean.

## OrientationService API

***viewOrientation***

Is a readonly property that return the current device orientation. Is type is DevOrientation and can be one of the following string

```typescript
    portrait-primary
    portrait-secondary
    landscape-primary
    landscape-secundary
```

***hasOrientationSensor()***

is a function that return a boolean that signals if device suports the orientation sensor.

***hasAbsoluteOrientationSensor()***

is a function that return a boolean that signals if device suports the absolute orientation sensor.

***orientation***

is a read/write property that represents the state of orientation event. Setting it to `true` or `false` it enables or disables the service orientation event.
Below an example of checking and enabling the orientation event

```typescript
if (!this.orsvc.orientation) {
    this.orsvc.orientation = true;
}
```

***absolute_orientation***

is a read/write property that represents the state of absolute orientation event. Setting it to `true` or `false` it enables or disables the service absolute orientation event.

## Directives

All directives requires the `OrientationService` events already enabled.

### - geoCompass

This is a simple use directive on HTML elements that support the `style.transform`. It rotates the element, pointing the top to north. It supports the device view change. It uses `OrientationService` absolute orientation.

```html
<div style="..." geoCompass>
    ...
</div>
```

### - pageCompass

This is a simple use directive on HTML elements that support the `style.transform`. It rotates the element, pointing the top ro the top of the page. It supports the device view change.

```html
<div style="..." pageCompass>
    ...
</div>
```

### - hSlide

### - bgSlide