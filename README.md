# Neat Angular

Table of contents

- [Angular change detection](#angular-change-detection)
- [Using getters and setters with Input decorator](#using-getters-and-setters-with-input-decorator)

## Angular change detection

### What is change detection?

The key technique of change detection is to execute checks for two states
- the current state and
- the new state.

If one of these states differs from the other, something has changed, meaning the view needs to be rendered.

Change detection in Angular has two parts:

1. Developer updates the data model
2. Angular re-renders the updated data model into the view

A data model in Angular can also change as a result of the following scenarios:
- DOM events (click, mouseenter, resize, etc.)
- Timers (setTimer, setInterval)
- XHR requests and promises

TODO: check if this is really all?
TODO: what about custom events? are these DOM or browser events https://www.google.com/search?q=browser+or+dom+event&rlz=1C5CHFA_enSI972SI972&oq=browser+or+dom+event&aqs=chrome..69i57j0i22i30j0i390l3.7743j0j1&sourceid=chrome&ie=UTF-8

The way Angular runs the change detection is by starting at the top and working its way down, reaching every component in the component tree.

### Change detection strategies

Angular provides two strategies to run change detection:
- default strategy and
- OnPush strategy.

#### Default strategy

TBD

#### OnPush strategy

TBD







In Angular, when you change any of your models, the framework detects the changes and immediately updates the relevant views. This ensures the underlying views are always in sync.


![Image](https://cdn-images-1.medium.com/max/1600/1*yKJIjqyGm5GhwOG61YVQxQ.png)

<!--
```typescript
ngOnInit() {
  // ...
  Plotly.newPlot('chart', data); // uses mousemove and mouseup event listeners
}
```
-->




Performance patterns

1. Zone pollution

- Problem: The Angular zone wraps callbacks that trigger redundant change detection cycles
- Identification: Often coming from 3rd party libraries with triggers request animation frame, setTimeout etc.
- Resolution: Move initialisations outside of Angular zone


https://carbon.now.sh/?bg=rgba%252874%252C144%252C226%252C1%2529&t=material&wt=none&l=auto&width=680&ds=true&dsyoff=20px&dsblur=68px&wc=true&wa=false&pv=56px&ph=56px&ln=false&fl=1&fm=Fira+Code&fs=14px&lh=152%2525&si=false&es=2x&wm=false

Before

```typescript
ngOnInit() {
  // ...
  Plotly.newPlot('chart', data); // uses mousemove and mouseup event listeners
}
```

After

```typescript
constructor(private zone: NgZone) {}

ngOnInit() {
  // ...
  this.zone.runOutsideAngular(() => Plotly.newPlot('chart', data)); // not triggering change detections anymore
}
```

2. TODO does it really makes sense?

Sources:

[Understanding Angular Change Detection Strategy](https://blogs.halodoc.io/understanding-angular-change-detection-strategy/) by Brilyan Aro
[Angular change detection and runtime optimization](https://angular.io/guide/change-detection) by Minko Gechev
[Angular Change Detection and the OnPush Strategy](https://www.toptal.com/angular/angular-change-detection#:~:text=The%20main%20idea%20behind%20the,memory%20will%20have%20to%20change.) by Ahmet Shapiro-Erciyas
[Angular ChangeDetectorRef / Usage notes](https://angular.io/api/core/ChangeDetectorRef#usage-notes) by Angular






---

## Using getters and setters with Input decorator


NgOnChanges is a lifecycle hook provided by Angular that can be implemented by Angular Components through the OnChanges interface.

This will expose a way to listen to changes to a child component’s data-bound input properties provided by a parent component.

Every input change is also broadcast with the `ngOnChanges` lifecycle hook.


When to use OnChanges
  - When you want to group logic together
  - When the data-change logic and functionality depend on changes from any of the other inputs.
  - You need to know the previous and current value (after the change), and/or whether it is the first change - SimpleChanges.

Input Setters and Getters

When to use
You have a small number of inputs

Plus…

When you only want to listen to the changes in isolation
Changes to the component don’t depend on the value of the other inputs
You don’t need the information that SimpleChanges provides.

# Understand how change detection works in Angular
Remember that there are certain circumstances when Angular won’t detect changes to the input data regardless of the method used.

One circumstance is when you have ChangeDetectionStrategy set to OnPush.

<child [cate]="cate" [menu]="menu"></child>
<child [menu]="menu" [cate]="cate"></child>
// not same if you use setter

From video

To detect and react when an input property value changes:
  - ngOnChanges Life Cycle Hook
  - property setter

ngOnChanges
 - We get all the changes instead of just the changes related to a single property
 - Useful when multiple properties change

Property setter
  - property setter is specific to a given property, so we only get changes of that specific property
  - useful when you want to keep track of a single property

onPush? this is something different?
https://blog.angular-university.io/onpush-change-detection-how-it-works/


From stackoverflow:

UPDATE

as long as your component is using ChangedetectionStategy.OnPush now using a getter is fine but still avoid it if you are not using OnPush

From blog:

CONS Of Using Getter Functions with Input Decorator:
If we use this in the template, it will be called in every change detection cycle. The worst-case will be that it would be called repetitively when using the change detection default method instead of OnPush.

I also suggest that we should use these methods at the inputs only when you want to do some light-weight manipulations in the dumb component for just rendering the table (as mentioned below just using setter) and not more computations like making an API call, subscriptions, etc. So, make sure before using these methods, you are well aware of the purpose it would be used and how it would affect the application too.


```TypeScript
export class ProductComponent {
  // TBD
  // TODO: try with nested object
}
```

See live code example on [Stackblitz](https://stackblitz.com/edit/angular-ivy-fqqhm5?devToolsHeight=33&file=src%2Fapp%2Fapp.component.html,src%2Fapp%2Fproduct%2Fproduct.component.html,src%2Fapp%2Fproduct%2Fproduct.component.ts,src%2Fapp%2Fapp.component.ts).

Sources:
- [Using Getters and Setters in TypeScript and Angular](https://andrew-morozw.medium.com/using-getters-and-setters-in-typescript-and-angular-d478829461c8) by Andrey Morozov
- [Detecting @​Input changes in Angular with ngOnChanges and Setters](https://ultimatecourses.com/blog/detect-input-property-changes-ngonchanges-setters) by Todd Motto
- [Angular: Setters vs ngOnChanges - which one is better?](https://dev.to/angular/angular-setters-vs-ngonchanges-which-one-is-better-4f2b)
- [Accessor properties, represented by getter and setter](https://javascript.info/property-accessors)
- [A deep dive into Angular Inputs](https://medium.com/generic-ui/a-deep-dive-into-angular-inputs-5a7201bc37ef)
- [‘Dumb’ Angular — Input Setter/Getter vs NgOnChanges](https://javascript.plainenglish.io/dumb-angular-input-setter-getter-vs-ngonchanges-f30e61937926)
- [Angular property setter vs ngonchanges](https://www.youtube.com/watch?v=BYwfrSlJFfY&ab_channel=kudvenkat)
- [Why Angular Input Setter is Only Being Fired Once](https://levelup.gitconnected.com/why-angular-input-setter-is-only-being-fired-once-53f1a66b1716)
- [Advantages of TypeScript Getters and Setters in Angular](https://levelup.gitconnected.com/advantages-of-typescript-getters-and-setters-in-angular-668f4639ad0c)
