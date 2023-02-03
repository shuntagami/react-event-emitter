## Observer pattern

```js
// 被観察者
class Subject {
  constructor() {
    this.observers = [];
  }

  addObserver(observer) {
    this.observers.push(observer);
  }

  removeObserver(observer) {
    this.observers = this.observers.filter((o) => o !== observer);
  }

  notifyObservers() {
    this.observers.forEach((observer) => observer.update(this));
  }
}

// 観察者
class Observer {
  update(subject) {
    throw new Error("Subclass must implement");
  }
}

class ConcreteSubject extends Subject {
  constructor() {
    super();
    this.data = 0;
  }

  setData(value) {
    this.data = value;
    // データが変更されたときに観察者に通知する
    this.notifyObservers();
  }
}

class ConcreteObserver extends Observer {
  // データが変更されたときに呼び出される
  update(subject) {
    console.log(`Data has changed to ${subject.data}`);
  }
}

const subject = new ConcreteSubject();
const observer = new ConcreteObserver();
subject.addObserver(observer); // 観察者を被観察者に登録
subject.setData(1); // データを変更し、観察者に通知
```

## EventEmitter

Node.js では Observer パターンは EventEmitter によって実装される。EventEmitter 自体は Observer パターンにおける Subject として機能する。
EventEmitter に対する Observer は一般的にリスナと呼ばれる。

EventEmitter の主要なインスタンスメソッドは次のようなものです。

### on(event, listener)

指定したイベント（第一引数、文字列）に対する新しいリスナ（第二引数、コー
ルバック関数）を登録します。

### once(event, listener)

on()と同様にイベントに対するリスナを登録しますが、このリスナはイベン
トが 1 回発行されたら自動的に削除され、2 回目以降のイベント発行では実行
されません。

### off(event, listener)

指定したイベント（第一引数、文字列）に登録されたリスナ（第二引数、コー
ルバック関数）を削除します。

## emit(event[, ...args])

指定したイベント（第一引数、文字列）を指定した引数（第二引数以降、任意）
で発行します。

```js
const EventEmitter = require("events");

class Subject extends EventEmitter {
  constructor() {
    super();
  }

  setData(value) {
    this.data = value;
    // 指定したイベント（第一引数、文字列）を指定した引数（第二引数以降、任意）で発行
    this.emit("dataChanged", this.data);
  }
}

class Observer {
  constructor(subject) {
    this.subject = subject;
    // 指定したイベント（第一引数、文字列）に対する新しいリスナ（第二引数、コールバック関数）を登録
    this.subject.on("dataChanged", this.update.bind(this));
  }

  update(data) {
    console.log(`Data has changed to ${data}`);
  }
}

const subject = new Subject();
const observer = new Observer(subject); // 観察者を被観察者に登録
subject.setData(1);
```
