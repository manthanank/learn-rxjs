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

[Stackblitz Link](https://stackblitz.com/edit/angular-psyjew?file=src/main.ts)

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

[Stackblitz Link](https://stackblitz.com/edit/angular-qy1hve?file=src/main.ts)

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

**range** -

```typescript

```

**throwError** -

```typescript

```

**timer** -

```typescript

```

**iif** -

```typescript

```

[Back to top⤴️](#contents)

### Join Creation Operators

**combineLatest** -

```typescript

```

**concat** -

```typescript

```

**forkJoin** -

```typescript

```

**merge** -

```typescript

```

**partition** -

```typescript

```

**race** -

```typescript

```

**zip** -

```typescript

```

[Back to top⤴️](#contents)

### Transformation Operators

**buffer** -

```typescript

```

**bufferCount** -

```typescript

```

**bufferTime** -

```typescript

```

**bufferToggle** -

```typescript

```

**bufferWhen** -

```typescript

```

**concatMap** -

```typescript

```

**concatMapTo** -

```typescript

```

**exhaust** -

```typescript

```

**exhaustMap** -

```typescript

```

**expand** -

```typescript

```

**groupBy** -

```typescript

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

**mergeScan** -

```typescript

```

**pairwise** -

```typescript

```

**partition** -

```typescript

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

**scan** -

```typescript

```

**switchScan** -

```typescript

```

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

**switchMapTo** -

```typescript

```

**window** -

```typescript

```

**windowCount** -

```typescript

```

**windowTime** -

```typescript

```

**windowToggle** -

```typescript

```

**windowWhen** -

```typescript

```

[Back to top⤴️](#contents)

### Filtering Operators

**audit** -

```typescript

```

**auditTime** -

```typescript

```

**debounce** -

```typescript

```

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

**distinct** -

```typescript

```

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

**distinctUntilKeyChanged** -

```typescript

```

**elementAt** -

```typescript

```

**filter** -

```typescript

```

**first** -

```typescript

```

**ignoreElements** -

```typescript

```

**last** -

```typescript

```

**sample** -

```typescript

```

**sampleTime** -

```typescript

```

**single** -

```typescript

```

**skip** -

```typescript

```

**skipLast** -

```typescript

```

**skipUntil** -

```typescript

```

**skipWhile** -

```typescript

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

**takeLast** -

```typescript

```

**takeUntil** -

```typescript

```

**takeWhile** -

```typescript

```

**throttle** -

```typescript

```

**throttleTime** -

```typescript

```

[Back to top⤴️](#contents)

### Join Operators

**combineLatestAll** -

```typescript

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

delay

```typescript

```

delayWhen

```typescript

```

dematerialize

```typescript

```

materialize

```typescript

```

observeOn

```typescript

```

subscribeOn

```typescript

```

timeInterval

```typescript

```

timestamp

```typescript

```

timeout

```typescript

```

timeoutWith

```typescript

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

[Stackblitz Link](https://stackblitz.com/edit/angular-qh5szd?file=src/main.ts)

[Back to top⤴️](#contents)

### Conditional and Boolean Operators

**defaultIfEmpty** -

```typescript

```

**every** -

```typescript

```

**find** -

```typescript

```

**findIndex** -

```typescript

```

**isEmpty** -

```typescript

```

[Back to top⤴️](#contents)

### Mathematical and Aggregate Operators

**count** -

```typescript

```

**max** -

```typescript

```

**min** -

```typescript

```

**reduce** -

```typescript

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
