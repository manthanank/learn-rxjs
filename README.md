# Learn Rxjs

[![NPM Package](https://github.com/manthanank/learn-rxjs/actions/workflows/publish.yml/badge.svg)](https://github.com/manthanank/learn-rxjs/actions/workflows/publish.yml)
[![Releases](https://github.com/manthanank/learn-rxjs/actions/workflows/releases.yml/badge.svg)](https://github.com/manthanank/learn-rxjs/actions/workflows/releases.yml)
![npm](https://img.shields.io/npm/dw/learn-rxjs)
![npm](https://img.shields.io/npm/dm/learn-rxjs)
![npm](https://img.shields.io/npm/dy/learn-rxjs)
![npm](https://img.shields.io/npm/dt/learn-rxjs)

## Contents

- [Introduction](#rxjs-reactive-extensions-library-for-javascript)
- [Installation](#installation)
- [Importing](#importing)
- [Observables](#observables)
- [Observer](#observer)
- [Operators](#operators)
  - [Categories of operators](#categories-of-operators)
    - [Creation Operators](#creation-operators)
    - [Join Creation Operators](#join-creation-operators)
    - [Transformation Operators](#transformation-operators)
    - [Filtering Operators](#filtering-operators)
    - [Join Operators](#join-operators)
    - [Multicasting Operators](#multicasting-operators)
    - [Error Handling Operators](#error-handling-operators)
    - [Utility Operators](#utility-operators)
    - [Conditional and Boolean Operators](#conditional-and-boolean-operators)
    - [Mathematical and Aggregate Operators](#mathematical-and-aggregate-operators)
- [Subscription](#subscription)
- [Subject](#subject)
- [Scheduler](#scheduler)

## RxJS (Reactive Extensions Library for JavaScript)

RxJS is a library for reactive programming using Observables, to make it easier to compose asynchronous or callback-based code.

## Installation

```bash
npm install rxjs
```

## Importing

```typescript
'rxjs' - for example: import { of } from 'rxjs';

'rxjs/operators' - for example: import { map } from 'rxjs/operators';

'rxjs/ajax' - for example: import { ajax } from 'rxjs/ajax';

'rxjs/fetch' - for example: import { fromFetch } from 'rxjs/fetch';

'rxjs/webSocket' - for example: import { webSocket } from 'rxjs/webSocket';

'rxjs/testing' - for example: import { TestScheduler } from 'rxjs/testing';
```

## Observables

Observables are declarative which provide support for passing messages between publishers and subscribers.

```typescript
// pipe

// subscribe
```

```typescript
import { Observable } from 'rxjs';

const observable = new Observable((subscriber) => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);
  setTimeout(() => {
    subscriber.next(4);
    subscriber.complete();
  }, 1000);
});
```

```typescript
import { Observable } from 'rxjs';

const observable = new Observable((subscriber) => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);
  setTimeout(() => {
    subscriber.next(4);
    subscriber.complete();
  }, 1000);
});

console.log('just before subscribe');
observable.subscribe({
  next(x) {
    console.log('got value ' + x);
  },
  error(err) {
    console.error('something wrong occurred: ' + err);
  },
  complete() {
    console.log('done');
  },
});
console.log('just after subscribe');
```

## Observer

An Observer is a consumer of values delivered by an Observable. Observers are simply a set of callbacks, one for each type of notification delivered by the Observable: next, error, and complete.

```typescript
// next

// error

// complete
```

```typescript
const observer = {
  next: x => console.log('Observer got a next value: ' + x),
  error: err => console.error('Observer got an error: ' + err),
  complete: () => console.log('Observer got a complete notification'),
};
```

```typescript
observable.subscribe(observer);
```

## Operators

Operators are functions. There are two kinds of operators:

**Pipeable Operators** are the kind that can be piped to Observables using the syntax `observableInstance.pipe(operator())`.

**Creation Operators** are the other kind of operator, which can be called as standalone functions to create a new Observable.

## Categories of operators

### Creation Operators

**ajax** - It creates an observable for an Ajax request with either a request object with url, headers, etc or a string for a URL.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { ajax } from 'rxjs/ajax';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>ajax Example</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const githubUsers = `https://api.github.com/users?per_page=2`;

    const users = ajax(githubUsers);

    const subscribe = users.subscribe(
      (res) => console.log(res),
      (err) => console.error(err)
    );
  }
}

bootstrapApplication(App);
```

**bindCallback** - `bindCallback` operator is used to convert a callback-style function into an observable.

It allows you to work with asynchronous APIs that follow the Node.js-style callback pattern, where the last argument of a function is a callback function that is invoked with the result or error.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { bindCallback } from 'rxjs/ajax';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>bindCallback Example</h1>
    <button (click)="performAsyncOperation()">Perform Async Operation</button>
  `,
})
export class App implements OnInit {
  
  ngOnInit() {}

  performAsyncOperation() {
    const boundAsyncFunction = bindCallback(this.someAsyncFunction);

    boundAsyncFunction().subscribe(([error, result]) => {
      if (error) {
        console.error('An error occurred:', error);
      } else {
        console.log('Result:', result);
      }
    });
  }

  someAsyncFunction(callback: (error: any, result: any) => void) {
    // Simulating an asynchronous operation
    setTimeout(() => {
      const error = null;
      const result = 'Hello, world!';
      callback(error, result);
    }, 2000);
  }
}

bootstrapApplication(App);
```

**bindNodeCallback** - `bindNodeCallback` is a function that converts a Node.js-style callback function into an Observable.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { bindNodeCallback  } from 'rxjs/ajax';

// Assume we have a Node.js-style callback function for file reading
function readFileAsync(filePath: string, callback: (error: Error | null, data: string) => void) {
  // Some asynchronous file reading logic
  // Call the callback with the error (if any) and the file data
  setTimeout(() => {
    if (filePath === '/path/to/file.txt') {
      callback(null, 'File content goes here');
    } else {
      callback(new Error('File not found'), null);
    }
  }, 2000);
}

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>bindNodeCallback Example</h1>
    <div>{{ fileContent }}</div>
  `,
})
export class App implements OnInit {
  
  fileContent: string;

  ngOnInit() {
    const readFile = bindNodeCallback(readFileAsync);
    const filePath = '/path/to/file.txt';

    const readFile$ = readFile(filePath);

    readFile$.subscribe(
      (data: string) => {
        this.fileContent = data;
        console.log('File content:', data);
      },
      (error: Error) => {
        console.error('Error reading file:', error);
      }
    );
  }
}

bootstrapApplication(App);
```

**defer** - Creates an Observable that, on subscribe, calls an Observable factory to make an Observable for each new Observer.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { switchMap, defer, of, timer, merge } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `<h1>defer operator</h1>`,
})
export class App implements OnInit {

  ngOnInit() {
    const s1 = of(new Date()); //will capture current date time
    const s2 = defer(() => of(new Date())); //will capture date time at the moment of subscription

    console.log(new Date());

    timer(2000)
      .pipe(switchMap(_ => merge(s1, s2)))
      .subscribe(console.log);
  }
}

bootstrapApplication(App);
```

**empty** - Replaced with the EMPTY constant or scheduled (e.g. scheduled([], scheduler)). Will be removed in v8.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { empty } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
  <h1>empty operator</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const subscribe = empty().subscribe({
      next: () => console.log('Next'),
      complete: () => console.log('Complete!')
    });
  }
}

bootstrapApplication(App);
```

**from** - Creates an Observable from an Array, an array-like object, a Promise, an iterable object, or an Observable-like object.

```jsx
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { from } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
  <h1>from operator</h1>
  `,
})
export class App implements OnInit {
  data: any;

  ngOnInit() {
    const obj = from(['a', 'b', 'c', 'd']);

    obj.subscribe((res) => {
      console.log(res);
      this.data = res;
    });
  }
}

bootstrapApplication(App);
```

[Stackblitz Example Link](https://stackblitz.com/edit/angular-psyjew?file=src/main.ts)

**fromEvent** - Creates an Observable that emits events of a specific type coming from the given event target.

Example:

```jsx
import 'zone.js/dist/zone';
import { AfterViewInit, Component, ElementRef, ViewChild } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>fromEvent Example</h1>
    <button #add>Add</button>
    {{countVal}}
    <table>
      <tr *ngFor="let value of values">
        <td>{{value}}</td>
      </tr>
    </table>
  `,
})
export class App implements AfterViewInit {
  data: any;
  count = 0;
  values = [];
  countVal: any;

  @ViewChild('add') add: ElementRef;

  ngAfterViewInit() {
    let count = 0;
    fromEvent(this.add.nativeElement, 'click').subscribe((data) => {
      console.log(count++);
      this.countVal = count++;
      console.log(this.countVal);
      this.count++;
      this.values.push(this.count);
    });
  }
}

bootstrapApplication(App);
```

[Stackblitz Example Link](https://stackblitz.com/edit/angular-qy1hve?file=src/main.ts)

**fromEventPattern** - Creates an Observable from an arbitrary API for registering event handlers.

```typescript
import 'zone.js/dist/zone';
import { AfterViewInit, Component, ElementRef, ViewChild } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEventPattern, Subject } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <button (click)="startListening()">Start Listening</button>
    <button (click)="stopListening()">Stop Listening</button>
  `,
})
export class App implements AfterViewInit {
  private eventListener: EventListenerOrEventListenerObject;
  private eventSubject: Subject<Event>;

  ngOnInit() {
    this.eventSubject = new Subject<Event>();
    this.eventListener = (event: Event) => this.eventSubject.next(event);
  }

  ngOnDestroy() {
    this.eventSubject.complete();
  }

  startListening() {
    const observable = fromEventPattern(
      // Function to add the event listener
      (handler: EventListenerOrEventListenerObject) => {
        document.addEventListener('customEvent', handler);
      },
      // Function to remove the event listener
      (handler: EventListenerOrEventListenerObject) => {
        document.removeEventListener('customEvent', handler);
      }
    );

    observable.subscribe((event: Event) => {
      console.log('Event received:', event);
      // Handle the event as needed
    });

    this.eventSubject.subscribe((event: Event) => {
      document.dispatchEvent(event);
    });
  }

  stopListening() {
    this.eventSubject.complete();
  }
}

bootstrapApplication(App);
```

**generate** - Generates an observable sequence by running a state-driven loop producing the sequence's elements, using the specified scheduler to send out observer messages.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { generate } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>generate example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const result = generate(0, x => x < 3, x => x + 1, x => x);

    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**interval** - Creates an Observable that emits sequential numbers every specified interval of time, on a specified SchedulerLike.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { interval, take } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>interval operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const numbers = interval(1000);
    
    const takeFourNumbers = numbers.pipe(take(4));
    
    takeFourNumbers.subscribe(x => console.log('Next: ', x));
  }
}

bootstrapApplication(App);
```

**of** - Converts the arguments to an observable sequence.

```jsx
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { from, of } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
  <h1>of operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const obj = of('a', 'b', 'c', 'd');

    obj.subscribe((res) => {
      console.log(res);
    });
  }
}

bootstrapApplication(App);
```

**range** - Creates an Observable that emits a sequence of numbers within a specified range.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { range } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
  <h1>range operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    //emit 1-10 in sequence
    const source = range(1, 10);
    //output: 1,2,3,4,5,6,7,8,9,10
    const example = source.subscribe(val => console.log(val));
  }
}

bootstrapApplication(App);
```

[Stackblitz Example Link](https://stackblitz.com/edit/stackblitz-starters-7899ka?file=src%2Fmain.ts)

**throwError** - Creates an observable that will create an error instance and push it to the consumer as an error immediately upon subscription.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { throwError } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
  <h1>throwError operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    let errorCount = 0;
    
    const errorWithTimestamp$ = throwError(() => {
      const error: any = new Error(`This is error number ${ ++errorCount }`);
      error.timestamp = Date.now();
      return error;
    });
    
    errorWithTimestamp$.subscribe({
      error: err => console.log(err.timestamp, err.message)
    });
    
    errorWithTimestamp$.subscribe({
      error: err => console.log(err.timestamp, err.message)
    });
  }
}

bootstrapApplication(App);
```

[Stackblitz Example Link](https://stackblitz.com/edit/stackblitz-starters-mdhkry?file=src%2Fmain.ts)

**timer** - It is a creation operator used to create an observable that starts emitting the values after the timeout, and the value will keep increasing after each call.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { timer } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
  <h1>timer operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const source = timer(1000);
    //output: 0
    const subscribe = source.subscribe(val => console.log(val));
  }
}

bootstrapApplication(App);
```

[Stackblitz Example Link](https://stackblitz.com/edit/stackblitz-starters-6ylica?file=src%2Fmain.ts)

**iif** - Checks a boolean at subscription time, and chooses between one of two observable sources

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { iif, of, interval } from 'rxjs';
import { mergeMap } from 'rxjs/operators';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
  <h1>iif operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const r$ = of('R');
    const x$ = of('X');

    interval(1000)
      .pipe(mergeMap(v => iif(() => v % 4 === 0, r$, x$)))
      .subscribe(console.log);
  }
}

bootstrapApplication(App);
```

[Stackblitz Example Link](https://stackblitz.com/edit/stackblitz-starters-ek4ybc?file=src%2Fmain.ts)

[Back to top⤴️](#contents)

### Join Creation Operators

**combineLatest** - Combines multiple Observables to create an Observable whose values are calculated from the latest values of each of its input Observables.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { timer, combineLatest } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>combineLatest operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const firstTimer = timer(0, 1000); // emit 0, 1, 2... after every second, starting from now
    const secondTimer = timer(500, 1000); // emit 0, 1, 2... after every second, starting 0,5s from now
    const combinedTimers = combineLatest([firstTimer, secondTimer]);
    combinedTimers.subscribe(value => console.log(value));
  }
}

bootstrapApplication(App);
```

[Stackblitz RxJS Example Link](https://stackblitz.com/edit/rxjs-zhrajm?file=index.ts)

[Stackblitz Example Link](https://stackblitz.com/edit/stackblitz-starters-dknwwt?file=src%2Fmain.ts)

**concat** - Creates an output Observable which sequentially emits all values from the first given Observable and then moves on to the next.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { interval, take, range, concat } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>combineLatest operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const timer = interval(1000).pipe(take(4));
    const sequence = range(1, 10);
    const result = concat(timer, sequence);
    result.subscribe((x) => console.log(x));
  }
}

bootstrapApplication(App);
```

[Stackblitz RxJS Example Link](https://stackblitz.com/edit/rxjs-dvj3th?file=index.ts)

[Stackblitz Example Link](https://stackblitz.com/edit/stackblitz-starters-yshqh7?file=src%2Fmain.ts)

**forkJoin** - Accepts an Array of ObservableInput or a dictionary Object of ObservableInput and returns an Observable that emits either an array of values in the exact same order as the passed array, or a dictionary of values in the same shape as the passed dictionary.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { forkJoin, of, timer } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>forkJoin operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const observable = forkJoin({
      foo: of(1, 2, 3, 4),
      bar: Promise.resolve(8),
      baz: timer(4000),
    });
    observable.subscribe({
      next: (value) => console.log(value),
      complete: () => console.log('This is how it ends!'),
    });
  }
}

bootstrapApplication(App);
```

[Stackblitz RxJS Example Link](https://stackblitz.com/edit/rxjs-gehtea?file=index.html)

[Stackblitz Example Link](https://stackblitz.com/edit/stackblitz-starters-7lvb9w?file=src%2Fmain.ts)

**merge** - Creates an output Observable which concurrently emits all values from every given input Observable.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { merge, fromEvent, interval } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>merge operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const timer = interval(1000);
    const clicksOrTimer = merge(clicks, timer);
    clicksOrTimer.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

[Stackblitz RxJS Example Link](https://stackblitz.com/edit/rxjs-e4uy3s?file=index.html)

[Stackblitz Example Link](https://stackblitz.com/edit/stackblitz-starters-x8njvg?file=src%2Fmain.ts)

**partition** - Splits the source Observable into two, one with values that satisfy a predicate, and another with values that don't satisfy the predicate.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { of, partition } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>partition operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const observableValues = of(1, 2, 3, 4, 5, 6);
    const [evens$, odds$] = partition(observableValues, value => value % 2 === 0);
    
    odds$.subscribe(x => console.log('odds', x));
    evens$.subscribe(x => console.log('evens', x));
  }
}

bootstrapApplication(App);
```

[Stackblitz RxJS Example Link](https://stackblitz.com/edit/rxjs-u9s5gf?file=index.html)

[Stackblitz Example Link](https://stackblitz.com/edit/stackblitz-starters-tmvrze?file=src%2Fmain.ts)

**race** - Returns an observable that mirrors the first source observable to emit an item.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { interval, map, race } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>race operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const obs1 = interval(7000).pipe(map(() => 'slow one'));
    const obs2 = interval(3000).pipe(map(() => 'fast one'));
    const obs3 = interval(5000).pipe(map(() => 'medium one'));
  }
}

bootstrapApplication(App);
```

**zip** - Combines multiple Observables to create an Observable whose values are calculated from the values, in order, of each of its input Observables.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { of, zip, map } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>zip operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const age$ = of(27, 25, 29);
    const name$ = of('Foo', 'Bar', 'Beer');
    const isDev$ = of(true, true, false);
    
    zip(age$, name$, isDev$).pipe(
      map(([age, name, isDev]) => ({ age, name, isDev }))
    )
    .subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

[Back to top⤴️](#contents)

### Transformation Operators

**buffer** - Buffers the source Observable values until closingNotifier emits.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, interval, buffer } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>buffer operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const intervalEvents = interval(1000);
    const buffered = intervalEvents.pipe(buffer(clicks));
    buffered.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**bufferCount** - Buffers the source Observable values until the size hits the maximum bufferSize given.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, bufferCount } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>bufferCount operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const buffered = clicks.pipe(bufferCount(2));
    buffered.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**bufferTime** - Buffers the source Observable values for a specific time period.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, bufferTime } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>bufferTime operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const buffered = clicks.pipe(bufferTime(1000));
    buffered.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**bufferToggle** - Buffers the source Observable values starting from an emission from openings and ending when the output of closingSelector emits.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, interval, bufferToggle, EMPTY } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>bufferToggle operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const openings = interval(1000);
    const buffered = clicks.pipe(bufferToggle(openings, i =>
      i % 2 ? interval(500) : EMPTY
    ));
    buffered.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**bufferWhen** - Buffers the source Observable values, using a factory function of closing Observables to determine when to close, emit, and reset the buffer.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, bufferWhen, interval } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>bufferWhen operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const buffered = clicks.pipe(
      bufferWhen(() => interval(1000 + Math.random() * 4000))
    );
    buffered.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**concatMap** - Projects each source value to an Observable which is merged in the output Observable, in a serialized fashion waiting for each one to complete before merging the next.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, concatMap, interval, take } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>concatMap operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const result = clicks.pipe(
      concatMap(ev => interval(1000).pipe(take(4)))
    );
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**concatMapTo** - Projects each source value to the same Observable which is merged multiple times in a serialized fashion on the output Observable.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, concatMapTo, interval, take } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>concatMapTo operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const result = clicks.pipe(
      concatMapTo(interval(1000).pipe(take(4)))
    );
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**exhaust** - Renamed to exhaustAll.

**exhaustMap** - Projects each source value to an Observable which is merged in the output Observable only if the previous projected Observable has completed.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, exhaustMap, interval, take } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>exhaustMap operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const result = clicks.pipe(
      exhaustMap(() => interval(1000).pipe(take(5)))
    );
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**expand** - Recursively projects each source value to an Observable which is merged in the output Observable.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, map, expand, of, delay, take } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>expand operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const powersOfTwo = clicks.pipe(
      map(() => 1),
      expand(x => of(2 * x).pipe(delay(1000))),
      take(10)
    );
    powersOfTwo.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**groupBy** -

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { of, groupBy, mergeMap, reduce } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>groupBy operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    of(
      { id: 1, name: 'JavaScript' },
      { id: 2, name: 'Parcel' },
      { id: 2, name: 'webpack' },
      { id: 1, name: 'TypeScript' },
      { id: 3, name: 'TSLint' }
    ).pipe(
      groupBy(p => p.id),
      mergeMap(group$ => group$.pipe(reduce((acc, cur) => [...acc, cur], [])))
    )
    .subscribe(p => console.log(p));
  }
}

bootstrapApplication(App);
```

**map** - Applies a given project function to each value emitted by the source Observable, and emits the resulting values as an Observable.

```jsx
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { from, map } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>map operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const data = from([
      {
        id: 1,
      },
      {
        id: 2,
      },
      {
        id: 3,
      },
      {
        id: 4,
      },
      {
        id: 5,
      },
    ]);

    data.pipe(map((data) => data.id)).subscribe((res) => console.log(res));
  }
}

bootstrapApplication(App);
```

**mapTo** - Emits the given constant value on the output Observable every time the source Observable emits a value.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, mapTo } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>mapTo operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const greetings = clicks.pipe(mapTo('Hi'));

    greetings.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**mergeMap** - Projects each source value to an Observable which is merged in the output Observable.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { of, mergeMap, interval, map } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>mergeMap operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const letters = of('a', 'b', 'c');
    const result = letters.pipe(
      mergeMap(x => interval(1000).pipe(map(i => x + i)))
    );
    
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**mergeMapTo** - Projects each source value to the same Observable which is merged multiple times in the output Observable.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, mergeMapTo, interval } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>mergeMapTo operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const result = clicks.pipe(mergeMapTo(interval(1000)));

    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**mergeScan** - Applies an accumulator function over the source Observable where the accumulator function itself returns an Observable, then each intermediate Observable returned is merged into the output Observable.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, map, mergeScan, of } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>mergeScan operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const click$ = fromEvent(document, 'click');
    const one$ = click$.pipe(map(() => 1));
    const seed = 0;
    const count$ = one$.pipe(
      mergeScan((acc, one) => of(acc + one), seed)
    );
    
    count$.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**pairwise** - Groups pairs of consecutive emissions together and emits them as an array of two values.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, pairwise, map } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>pairwise operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent<PointerEvent>(document, 'click');
    const pairs = clicks.pipe(pairwise());
    const distance = pairs.pipe(
      map(([first, second]) => {
        const x0 = first.clientX;
        const y0 = first.clientY;
        const x1 = second.clientX;
        const y1 = second.clientY;
        return Math.sqrt(Math.pow(x0 - x1, 2) + Math.pow(y0 - y1, 2));
      })
    );
    
    distance.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**partition** - Splits the source Observable into two, one with values that satisfy a predicate, and another with values that don't satisfy the predicate.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { of, partition } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>partition operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const observableValues = of(1, 2, 3, 4, 5, 6);
    const [evens$, odds$] = partition(observableValues, value => value % 2 === 0);
    
    odds$.subscribe(x => console.log('odds', x));
    evens$.subscribe(x => console.log('evens', x));
  }
}

bootstrapApplication(App);
```

**pluck** - Maps each source value to its specified nested property.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { from, pluck, toArray } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>Pluck Example</h1>
    <div class="row">
      <div class="col">
        <ul *ngFor="let item of data1">
          <li>{{item}}</li>
        </ul>
      </div>
      <div class="col">
        <ul *ngFor="let item of data2">
          <li>{{item}}</li>
        </ul>
      </div>
    </div>
  `,
})
export class App implements OnInit {
  users = [
    {
      name: 'abc',
      age: '25',
      address: {
        state: 'DL',
        country: 'India',
      },
    },
    {
      name: 'efg',
      age: '25',
      address: {
        state: 'MH',
        country: 'India',
      },
    },
    {
      name: 'lmn',
      age: '25',
      address: {
        state: 'KA',
        country: 'India',
      },
    },
    {
      name: 'pqr',
      age: '25',
      address: {
        state: 'KL',
        country: 'India',
      },
    },
    {
      name: 'xyz',
      age: '25',
      address: {
        state: 'GA',
        country: 'India',
      },
    },
  ];
  data1: any;
  data2: any;
  constructor() {}

  ngOnInit() {
    from(this.users)
      .pipe(pluck('name'), toArray())
      .subscribe((res) => {
        console.log(res);
        this.data1 = res;
      });
    from(this.users)
      .pipe(pluck('address', 'state'), toArray())
      .subscribe((res) => {
        console.log(res);
        this.data2 = res;
      });
  }
}

bootstrapApplication(App);
```

**scan** - Useful for encapsulating and managing state. Applies an accumulator (or "reducer function") to each value from the source after an initial state is established -- either via a seed value (second argument), or from the first value from the source.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { of, scan, map } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>scan operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const numbers$ = of(1, 2, 3);
    
    numbers$
      .pipe(
        // Get the sum of the numbers coming in.
        scan((total, n) => total + n),
        // Get the average by dividing the sum by the total number
        // received so far (which is 1 more than the zero-based index).
        map((sum, index) => sum / (index + 1))
      )
      .subscribe(console.log);
  }
}

bootstrapApplication(App);
```

[Stackblitz Example Link](https://stackblitz.com/edit/stackblitz-starters-yk2lwk?file=src%2Fmain.ts)

**switchScan** - Applies an accumulator function over the source Observable where the accumulator function itself returns an Observable, emitting values only from the most recently returned Observable.

**switchMap** - Projects each source value to an Observable which is merged in the output Observable, emitting values only from the most recently projected Observable.

```jsx
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { from, of, switchMap } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>switchMap operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const data = from(['abc', 'xyz', 'efg', 'pqr', 'lmn']);

    data
      .pipe(switchMap((data) => this.getData(data)))
      .subscribe((res) => console.log(res));
  }

  getData(data) {
    return of('name is' + data);
  }
}

bootstrapApplication(App);
```

**switchMapTo** - Projects each source value to the same Observable which is flattened multiple times with switchMap in the output Observable.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, switchMapTo, interval } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>switchMapTo operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const result = clicks.pipe(switchMapTo(interval(1000)));
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

[Stackblitz Example Link](https://stackblitz.com/edit/stackblitz-starters-wbj1xt?file=src%2Fmain.ts)

**window** - Branch out the source Observable values as a nested Observable whenever windowBoundaries emits.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, interval, window, map, take, mergeAll } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>window operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const sec = interval(1000);
    const result = clicks.pipe(
      window(sec),
      map(win => win.pipe(take(2))), // take at most 2 emissions from each window
      mergeAll()                     // flatten the Observable-of-Observables
    );
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

[Stackblitz Example Link](https://stackblitz.com/edit/stackblitz-starters-q7tjwj?file=src%2Fmain.ts)

**windowCount** - Branch out the source Observable values as a nested Observable with each nested Observable emitting at most windowSize values.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, windowCount, map, skip, mergeAll } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>windowCount operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const result = clicks.pipe(
      windowCount(3),
      map(win => win.pipe(skip(1))), // skip first of every 3 clicks
      mergeAll()                     // flatten the Observable-of-Observables
    );
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

[Stackblitz Example 1 Link](https://stackblitz.com/edit/stackblitz-starters-xpy6gh?file=src%2Fmain.ts)

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, windowCount, mergeAll } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>windowCount operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const result = clicks.pipe(
      windowCount(2, 3),
      mergeAll() // flatten the Observable-of-Observables
    );
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

[Stackblitz Example 2 Link](https://stackblitz.com/edit/stackblitz-starters-q7tjwj?file=src%2Fmain.ts)

**windowTime** - Branch out the source Observable values as a nested Observable periodically in time.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, windowTime, map, take, mergeAll } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>windowTime operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const result = clicks.pipe(
      windowTime(1000),
      map(win => win.pipe(take(2))), // take at most 2 emissions from each window
      mergeAll()                     // flatten the Observable-of-Observables
    );
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

[Stackblitz Example 1 Link](https://stackblitz.com/edit/stackblitz-starters-uvwhnp?file=src%2Fmain.ts)

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, windowTime, map, take, mergeAll } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>windowTime operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const result = clicks.pipe(
      windowTime(1000, 5000),
      map(win => win.pipe(take(2))), // take at most 2 emissions from each window
      mergeAll()                     // flatten the Observable-of-Observables
    );
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

[Stackblitz Example 2 Link](https://stackblitz.com/edit/stackblitz-starters-mcsaht?file=src%2Fmain.ts)

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, windowTime, mergeAll } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>windowTime operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const result = clicks.pipe(
      windowTime(1000, 5000, 2), // take at most 2 emissions from each window
      mergeAll()                 // flatten the Observable-of-Observables
    );
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

[Stackblitz Example 3 Link](https://stackblitz.com/edit/stackblitz-starters-x1qyak?file=src%2Fmain.ts)

**windowToggle** - Branch out the source Observable values as a nested Observable starting from an emission from openings and ending when the output of closingSelector emits.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, interval, windowToggle, EMPTY, mergeAll } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>windowToggle operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const openings = interval(1000);
    const result = clicks.pipe(
      windowToggle(openings, i => i % 2 ? interval(500) : EMPTY),
      mergeAll()
    );
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

[Stackblitz Example Link](https://stackblitz.com/edit/stackblitz-starters-qdmr61?file=src%2Fmain.ts)

**windowWhen** - Branch out the source Observable values as a nested Observable using a factory function of closing Observables to determine when to start a new window.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, windowWhen, interval, map, take, mergeAll } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>windowWhen operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const result = clicks.pipe(
      windowWhen(() => interval(1000 + Math.random() * 4000)),
      map(win => win.pipe(take(2))), // take at most 2 emissions from each window
      mergeAll()                     // flatten the Observable-of-Observables
    );
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

[Stackblitz Example 1 Link](https://stackblitz.com/edit/stackblitz-starters-vjykyp?file=src%2Fmain.ts)

[Back to top⤴️](#contents)

### Filtering Operators

**audit** - Ignores source values for a duration determined by another Observable, then emits the most recent value from the source Observable, then repeats this process.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, audit, interval } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>audit operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const result = clicks.pipe(audit(ev => interval(1000)));
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

[Stackblitz Example Link](https://stackblitz.com/edit/stackblitz-starters-xskoee?file=src%2Fmain.ts)

**auditTime** - Ignores source values for duration milliseconds, then emits the most recent value from the source Observable, then repeats this process.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, auditTime } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>auditTime operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const result = clicks.pipe(auditTime(1000));
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

[Stackblitz Example Link](https://stackblitz.com/edit/stackblitz-starters-dcpyww?file=src%2Fmain.ts)

**debounce** - Emits a notification from the source Observable only after a particular time span determined by another Observable has passed without another source emission.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, scan, debounce, interval } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>debounce operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const result = clicks.pipe(
      scan(i => ++i, 1),
      debounce(i => interval(200 * i))
    );
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

[Stackblitz Example Link](https://stackblitz.com/edit/stackblitz-starters-o6khgs?file=src%2Fmain.ts)

**debounceTime** - Emits a notification from the source Observable only after a particular time span has passed without another source emission.

```typescript
import 'zone.js/dist/zone';
import {
  AfterViewInit,
  Component,
  ElementRef,
  OnInit,
  ViewChild,
} from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, map, debounceTime } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>debounceTime Example</h1>
    <input type="text" #myInput />
    <p *ngIf="requestedData != null">Data: {{requestedData}}</p>
  `,
})
export class App implements OnInit, AfterViewInit {
  requestedData = null;
  @ViewChild('myInput') myInput: ElementRef;
  constructor() {}

  ngOnInit() {}

  ngAfterViewInit() {
    const searchItem = fromEvent<any>(this.myInput.nativeElement, 'keyup').pipe(
      map((event) => {
        event.target.value;
      }),
      debounceTime(1000)
    );
    searchItem.subscribe((res) => {
      console.log(res);
      this.requestedData = res;

      setTimeout(() => {
        this.requestedData = null;
      }, 2000);
    });
  }
}

bootstrapApplication(App);
```

**distinct** - Returns an Observable that emits all items emitted by the source Observable that are distinct by comparison from previous items.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { of, distinct } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>distinct operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    of(1, 1, 2, 2, 2, 1, 2, 3, 4, 3, 2, 1)
      .pipe(distinct())
      .subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

[Stackblitz Example Link](https://stackblitz.com/edit/stackblitz-starters-ehgdax?file=src%2Fmain.ts)

**distinctUntilChanged** - Returns a result Observable that emits all values pushed by the source observable if they are distinct in comparison to the last value the result observable emitted.

```typescript
import 'zone.js/dist/zone';
import {
  AfterViewInit,
  Component,
  ElementRef,
  OnInit,
  ViewChild,
} from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, map, debounceTime, distinctUntilChanged } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>distinctUntilChanged Example</h1>
    <input type="text" #myInput />
    <p *ngIf="requestedData != null">Data: {{requestedData}}</p>
  `,
})
export class App implements OnInit, AfterViewInit {
  requestedData = null;
  @ViewChild('myInput') myInput: ElementRef;
  constructor() {}

  ngOnInit() {}

  ngAfterViewInit() {
    const searchItem = fromEvent<any>(this.myInput.nativeElement, 'keyup').pipe(
      map((event) => {
        event.target.value;
      }),
      debounceTime(500),
      distinctUntilChanged()
    );
    searchItem.subscribe((res) => {
      console.log(res);
      this.requestedData = res;

      setTimeout(() => {
        this.requestedData = null;
      }, 2000);
    });
  }
}

bootstrapApplication(App);
```

**distinctUntilKeyChanged** - Returns an Observable that emits all items emitted by the source Observable that are distinct by comparison from the previous item, using a property accessed by using the key provided to check if the two items are distinct.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { of, distinctUntilKeyChanged } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>distinctUntilKeyChanged Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    of(
      { age: 4, name: 'Foo' },
      { age: 7, name: 'Bar' },
      { age: 5, name: 'Foo' },
      { age: 6, name: 'Foo' }
    ).pipe(
      distinctUntilKeyChanged('name')
    )
    .subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

[Stackblitz RxJS Example Link](https://stackblitz.com/edit/rxjs-754d9b?file=index.html)

[Stackblitz Angular Example Link](https://stackblitz.com/edit/stackblitz-starters-jtriop?file=src%2Fmain.ts)

**elementAt** - Emits the single value at the specified index in a sequence of emissions from the source Observable.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, elementAt } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>elementAt Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const result = clicks.pipe(elementAt(2));
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**filter** - Filter items emitted by the source Observable by only emitting those that satisfy a specified predicate.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, filter } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>filter Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const div = document.createElement('div');
    div.style.cssText = 'width: 200px; height: 200px; background: #09c;';
    document.body.appendChild(div);

    const clicks = fromEvent(document, 'click');
    const clicksOnDivs = clicks.pipe(filter(ev => (<HTMLElement>ev.target).tagName === 'DIV'));
    clicksOnDivs.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**first** - Emits only the first value (or the first value that meets some condition) emitted by the source Observable.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, first } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>first Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const result = clicks.pipe(first());
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**ignoreElements** - Ignores all items emitted by the source Observable and only passes calls of complete or error.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { of, ignoreElements } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>ignoreElements Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    of('you', 'talking', 'to', 'me')
      .pipe(ignoreElements())
      .subscribe({
        next: word => console.log(word),
        error: err => console.log('error:', err),
        complete: () => console.log('the end'),
      });
  }
}

bootstrapApplication(App);
```

**last** - Returns an Observable that emits only the last item emitted by the source Observable. It optionally takes a predicate function as a parameter, in which case, rather than emitting the last item from the source Observable, the resulting Observable will emit the last item from the source Observable that satisfies the predicate.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { from, last } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>last Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const source = from(['x', 'y', 'z']);
    const result = source.pipe(last());

    result.subscribe(value => console.log(`Last alphabet: ${ value }`));
  }
}

bootstrapApplication(App);
```

**sample** - Emits the most recently emitted value from the source Observable whenever another Observable, the notifier, emits.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, interval, sample } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>sample Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const seconds = interval(1000);
    const clicks = fromEvent(document, 'click');
    const result = seconds.pipe(sample(clicks));

    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**sampleTime** - Emits the most recently emitted value from the source Observable within periodic time intervals.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, sampleTime } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>sampleTime Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const result = clicks.pipe(sampleTime(1000));

    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**single** - Returns an observable that asserts that only one value is emitted from the observable that matches the predicate. If no predicate is provided, then it will assert that the observable only emits one value.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { of, single } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>single Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const source1 = of(
    { name: 'Ben' },
    { name: 'Tracy' },
    { name: 'Laney' },
    { name: 'Lily' }
    );
    
    source1
      .pipe(single(x => x.name.startsWith('B')))
      .subscribe(x => console.log(x));
    // Emits 'Ben'
    
    
    const source2 = of(
    { name: 'Ben' },
    { name: 'Tracy' },
    { name: 'Bradley' },
    { name: 'Lincoln' }
    );
    
    source2
      .pipe(single(x => x.name.startsWith('B')))
      .subscribe({ error: err => console.error(err) });
    // Error emitted: SequenceError('Too many values match')
    
    
    const source3 = of(
    { name: 'Laney' },
    { name: 'Tracy' },
    { name: 'Lily' },
    { name: 'Lincoln' }
    );
    
    source3
      .pipe(single(x => x.name.startsWith('B')))
      .subscribe({ error: err => console.error(err) });
    // Error emitted: NotFoundError('No values match')
  }
}

bootstrapApplication(App);
```

**skip** -Returns an Observable that skips the first count items emitted by the source Observable.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { interval, skip } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>skip Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    // emit every half second
    const source = interval(500);
    // skip the first 10 emitted values
    const result = source.pipe(skip(10));

    result.subscribe(value => console.log(value));
  }
}

bootstrapApplication(App);
```

**skipLast** - Skip a specified number of values before the completion of an observable.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { of, skipLast } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>skipLast Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const numbers = of(1, 2, 3, 4, 5);
    const skipLastTwo = numbers.pipe(skipLast(2));
    skipLastTwo.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**skipUntil** - Returns an Observable that skips items emitted by the source Observable until a second Observable emits an item.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { interval, fromEvent, skipUntil } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>skipUntil Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const intervalObservable = interval(1000);
    const click = fromEvent(document, 'click');

    const emitAfterClick = intervalObservable.pipe(
      skipUntil(click)
    );
    // clicked at 4.6s. output: 5...6...7...8........ or
    // clicked at 7.3s. output: 8...9...10..11.......
    emitAfterClick.subscribe(value => console.log(value));
  }
}

bootstrapApplication(App);
```

**skipWhile** - Returns an Observable that skips all items emitted by the source Observable as long as a specified condition holds true, but emits all further source items as soon as the condition becomes false.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { from, skipWhile } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>skipWhile Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const source = from(['Green Arrow', 'SuperMan', 'Flash', 'SuperGirl', 'Black Canary'])
    // Skip the heroes until SuperGirl
    const example = source.pipe(skipWhile(hero => hero !== 'SuperGirl'));
    // output: SuperGirl, Black Canary
    example.subscribe(femaleHero => console.log(femaleHero));
  }
}

bootstrapApplication(App);
```

**take** - Emits only the first count values emitted by the source Observable.

```jsx
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { from, interval, take } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>take Example</h1>
  `,
})
export class App implements OnInit {
  data = ['a', 'b', 'c', 'd'];
  ngOnInit() {
    const datas = from(this.data);

    datas.pipe(take(3)).subscribe((res) => {
      console.log(res);
    });
  }
}

bootstrapApplication(App);
```

**takeLast** - Waits for the source to complete, then emits the last N values from the source, as specified by the count argument.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { range, takeLast } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>takeLast Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const many = range(1, 100);
    const lastThree = many.pipe(takeLast(3));
    lastThree.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**takeUntil** - Emits the values emitted by the source Observable until a notifier Observable emits a value.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { interval, fromEvent, takeUntil } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>takeUntil Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const source = interval(1000);
    const clicks = fromEvent(document, 'click');
    const result = source.pipe(takeUntil(clicks));
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**takeWhile** - Emits values emitted by the source Observable so long as each value satisfies the given predicate, and then completes as soon as this predicate is not satisfied.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, takeWhile } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>takeWhile Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const clicks = fromEvent<PointerEvent>(document, 'click');
    const result = clicks.pipe(takeWhile(ev => ev.clientX > 200));
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**throttle** - Emits a value from the source Observable, then ignores subsequent source values for a duration determined by another Observable, then repeats this process.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, throttle, interval } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>throttle Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const result = clicks.pipe(throttle(() => interval(1000)));

    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**throttleTime** - Emits a value from the source Observable, then ignores subsequent source values for duration milliseconds, then repeats this process.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, throttleTime } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>throttleTime Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const result = clicks.pipe(throttleTime(1000));

    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

[Back to top⤴️](#contents)

### Join Operators

**combineLatestAll** - Flattens an Observable-of-Observables by applying combineLatest when the Observable-of-Observables completes.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, map, interval, take, combineLatestAll } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>combineLatestAll Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const higherOrder = clicks.pipe(
      map(() => interval(Math.random() * 2000).pipe(take(3))),
      take(2)
    );
    const result = higherOrder.pipe(combineLatestAll());

    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**concatAll** -

```typescript

```

**exhaustAll** -

```typescript

```

**mergeAll** -

```typescript

```

**switchAll** -

```typescript

```

**startWith** -

```typescript

```

**withLatestFrom** -

```typescript

```

### Multicasting Operators

**multicast** -

```typescript

```

**publish** -

```typescript

```

**publishBehavior** -

```typescript

```

**publishLast** -

```typescript

```

**publishReplay** -

```typescript

```

**share** -

```typescript

```

[Back to top⤴️](#contents)

### Error Handling Operators

**catchError** -

```typescript

```

**retry** -

```typescript

```

**retryWhen** -

```typescript

```

[Back to top⤴️](#contents)

### Utility Operators

**tap** - Used to perform side-effects for notifications from the source observable.

```jsx
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { interval, Subscription, tap } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>tap Example</h1>
  `,
})
export class App implements OnInit {
  subscription: Subscription;

  ngOnInit() {
    let count = interval(1000);

    this.subscription = count
      .pipe(
        tap((res) => {
          console.log('Before tap', res);
          if (res == 5) {
            this.subscription.unsubscribe();
          }
        })
      )
      .subscribe((res) => {
        console.log('After tap', res);
      });
  }
}

bootstrapApplication(App);
```

**delay** - Delays the emission of items from the source Observable by a given timeout or until a given Date.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, delay } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>delay Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const delayedClicks = clicks.pipe(delay(1000)); // each click emitted after 1 second
    delayedClicks.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**delayWhen** - Delays the emission of items from the source Observable by a given time span determined by the emissions of another Observable.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, delay } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>delayWhen Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const delayedClicks = clicks.pipe(
      delayWhen(() => interval(Math.random() * 5000))
    );
    delayedClicks.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**dematerialize** - Converts an Observable of ObservableNotification objects into the emissions that they represent.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { NextNotification, ErrorNotification, of, dematerialize } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>dematerialize Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const notifA: NextNotification<string> = { kind: 'N', value: 'A' };
    const notifB: NextNotification<string> = { kind: 'N', value: 'B' };
    const notifE: ErrorNotification = { kind: 'E', error: new TypeError('x.toUpperCase is not a function') };
    
    const materialized = of(notifA, notifB, notifE);
    
    const upperCase = materialized.pipe(dematerialize());
    upperCase.subscribe({
      next: x => console.log(x),
      error: e => console.error(e)
    });
  }
}

bootstrapApplication(App);
```

**materialize** - Represents all of the notifications from the source Observable as next emissions marked with their original types within Notification objects.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { of, materialize, map } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>materialize Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const letters = of('a', 'b', 13, 'd');
    const upperCase = letters.pipe(map((x: any) => x.toUpperCase()));
    const materialized = upperCase.pipe(materialize());
    
    materialized.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**observeOn** - Re-emits all notifications from source Observable with specified scheduler.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { interval, observeOn, animationFrameScheduler } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>observeOn Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const someDiv = document.createElement('div');
    someDiv.style.cssText = 'width: 200px;background: #09c';
    document.body.appendChild(someDiv);
    const intervals = interval(10);      // Intervals are scheduled
                                        // with async scheduler by default...
    intervals.pipe(
      observeOn(animationFrameScheduler) // ...but we will observe on animationFrame
    )                                    // scheduler to ensure smooth animation.
    .subscribe(val => {
      someDiv.style.height = val + 'px';
    });
  }
}

bootstrapApplication(App);
```

**subscribeOn** - Asynchronously subscribes Observers to this Observable on the specified SchedulerLike.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { of, merge } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>subscribeOn Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const a = of(1, 2, 3);
    const b = of(4, 5, 6);
    
    merge(a, b).subscribe(console.log);
  }
}

bootstrapApplication(App);
```

**timeInterval** - Emits an object containing the current value, and the time that has passed between emitting the current value and the previous value, which is calculated by using the provided scheduler's now() method to retrieve the current time at each emission, then calculating the difference. The scheduler defaults to asyncScheduler, so by default, the interval will be in milliseconds.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { interval, timeInterval } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>timeInterval Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const seconds = interval(1000);
    
    seconds
      .pipe(timeInterval())
      .subscribe(value => console.log(value));
  }
}

bootstrapApplication(App);
```

**timestamp** - Attaches a timestamp to each item emitted by an observable indicating when it was emitted

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, timestamp } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>timestamp Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const clickWithTimestamp = fromEvent(document, 'click').pipe(
      timestamp()
    );

    // Emits data of type { value: PointerEvent, timestamp: number }
    clickWithTimestamp.subscribe(data => {
      console.log(data);
    });
  }
}

bootstrapApplication(App);
```

**timeout** - Errors if Observable does not emit a value in given time span.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, delay } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>timeout Example</h1>
  `,
})

class CustomTimeoutError extends Error {
  constructor() {
    super('It was too slow');
    this.name = 'CustomTimeoutError';
  }
}

export class App implements OnInit {

  ngOnInit() {
    const slow$ = interval(900);
    
    slow$.pipe(
      timeout({
        each: 1000,
        with: () => throwError(() => new CustomTimeoutError())
      })
    )
    .subscribe({
      error: console.error
    });
  }
}

bootstrapApplication(App);
```

**timeoutWith** - When the passed timespan elapses before the source emits any given value, it will unsubscribe from the source, and switch the subscription to another observable.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { interval, timeoutWith } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>timeoutWith Example</h1>
  `,
})
export class App implements OnInit {

  ngOnInit() {
    const slow$ = interval(1000);
    const faster$ = interval(500);

    slow$
      .pipe(timeoutWith(900, faster$))
      .subscribe(console.log);
  }
}

bootstrapApplication(App);
```

**toArray** - Collects all source emissions and emits them as an array when the source completes.

Example:

```jsx
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { from, interval, of, Subscription, take, toArray } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>toArray Example</h1>
  `,
})
export class App implements OnInit {
  subscription: Subscription;

  data = [
    { id: 1, name: 'abc' },
    { id: 2, name: 'efg' },
    { id: 3, name: 'lmn' },
    { id: 4, name: 'pqr' },
    { id: 5, name: 'xyz' },
  ];

  ngOnInit() {
    //eaxample 1
    const count = interval(1000);
    this.subscription = count.pipe(take(5), toArray()).subscribe((res) => {
      console.log(res);
    });

    //exampe 2
    const datas = from(this.data);
    this.subscription = datas.pipe(toArray()).subscribe((res) => {
      console.log(res);
    });

    //example 3
    const data = of('a', 'b', 'c');
    this.subscription = data.pipe(toArray()).subscribe((res) => {
      console.log(res);
    });
  }
}

bootstrapApplication(App);
```

[Stackblitz Example Link](https://stackblitz.com/edit/angular-qh5szd?file=src/main.ts)

[Back to top⤴️](#contents)

### Conditional and Boolean Operators

**defaultIfEmpty** - Emits a given value if the source Observable completes without emitting any next value, otherwise mirrors the source Observable.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, takeUntil, interval, defaultIfEmpty } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>defaultIfEmpty operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicks = fromEvent(document, 'click');
    const clicksBeforeFive = clicks.pipe(takeUntil(interval(5000)));
    const result = clicksBeforeFive.pipe(defaultIfEmpty('no clicks'));
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**every** - Returns an Observable that emits whether or not every item of the source satisfies the condition specified.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { of, every } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>every operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    of(1, 2, 3, 4, 5, 6)
      .pipe(every(x => x < 5))
      .subscribe(x => console.log(x)); // -> false
  }
}

bootstrapApplication(App);
```

**find** - Emits only the first value emitted by the source Observable that meets some condition.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, find } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>find operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const div = document.createElement('div');
    div.style.cssText = 'width: 200px; height: 200px; background: #09c;';
    document.body.appendChild(div);

    const clicks = fromEvent(document, 'click');
    const result = clicks.pipe(find(ev => (<HTMLElement>ev.target).tagName === 'DIV'));
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**findIndex** - Emits only the index of the first value emitted by the source Observable that meets some condition.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, findIndex } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>findIndex operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const div = document.createElement('div');
    div.style.cssText = 'width: 200px; height: 200px; background: #09c;';
    document.body.appendChild(div);

    const clicks = fromEvent(document, 'click');
    const result = clicks.pipe(findIndex(ev => (<HTMLElement>ev.target).tagName === 'DIV'));
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**isEmpty** - Emits false if the input Observable emits any values, or emits true if the input Observable completes without emitting any values.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { Subject, isEmpty } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>isEmpty operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const source = new Subject<string>();
    const result = source.pipe(isEmpty());
    
    source.subscribe(x => console.log(x));
    result.subscribe(x => console.log(x));

    source.next('a');
    source.next('b');
    source.next('c');
    source.complete();
  }
}

bootstrapApplication(App);
```

[Back to top⤴️](#contents)

### Mathematical and Aggregate Operators

**count** - Counts the number of emissions on the source and emits that number when the source completes.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { interval, fromEvent, takeUntil, count } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>count operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const seconds = interval(1000);
    const clicks = fromEvent(document, 'click');
    const secondsBeforeClick = seconds.pipe(takeUntil(clicks));
    const result = secondsBeforeClick.pipe(count());
    result.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**max** - The max operator operates on an Observable that emits numbers (or items that can be compared with a provided function), and when source Observable completes it emits a single item: the item with the largest value.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { of, max } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>max operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    of(5, 4, 7, 2, 8)
      .pipe(max())
      .subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**min** - The min operator operates on an Observable that emits numbers (or items that can be compared with a provided function), and when source Observable completes it emits a single item: the item with the smallest value.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { of, min } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>min operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    of(5, 4, 7, 2, 8)
      .pipe(min())
      .subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

**reduce** - Applies an accumulator function over the source Observable, and returns the accumulated result when the source completes, given an optional seed value.

```typescript
import 'zone.js/dist/zone';
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { bootstrapApplication } from '@angular/platform-browser';
import { fromEvent, takeUntil, interval, map, reduce } from 'rxjs';

@Component({
  selector: 'my-app',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>reduce operator</h1>
  `,
})
export class App implements OnInit {
  ngOnInit() {
    const clicksInFiveSeconds = fromEvent(document, 'click')
      .pipe(takeUntil(interval(5000)));

    const ones = clicksInFiveSeconds.pipe(map(() => 1));
    const seed = 0;
    const count = ones.pipe(reduce((acc, one) => acc + one, seed));

    count.subscribe(x => console.log(x));
  }
}

bootstrapApplication(App);
```

[Back to top⤴️](#contents)

## Subscription

A Subscription is an object that represents a disposable resource, usually the execution of an Observable.

## Subject

An RxJS Subject is a special type of Observable that allows values to be multicasted to many Observers.

## Scheduler

A scheduler controls when a subscription starts and when notifications are delivered. It consists of three components.

A Scheduler is a data structure. It knows how to store and queue tasks based on priority or other criteria.

A Scheduler is an execution context. It denotes where and when the task is executed (e.g. immediately, or in another callback mechanism such as setTimeout or process.nextTick, or the animation frame).

A Scheduler has a (virtual) clock. It provides a notion of "time" by a getter method now() on the scheduler. Tasks being scheduled on a particular scheduler will adhere only to the time denoted by that clock.

[Back to top⤴️](#contents)
