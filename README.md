#throttleTime in RxJS

We all know that the Reactive Extensions for JavaScript (RxJS) is a library for composing asynchronous and event-based programs. RxJS comes with wide range of operators and in this blog we will discuss about `throttleTime` which is one of the operators.

Before I move to `throttleTime`, lets understand what `throttling` means. So in simple terms `throttling` means to control the rate at which a process is conducted. So if you have situation when you want to start a process, then wait for `x` time and then resume and repeat the process, you would need to `throttle` that process.

Coming to an application level use case, suppose there is a situation when you want to abstain a user from continously firing events, `throttleTime` can be helpful in such situations. A couple of such scenarios can be :

* Allowing a user to click a button only once per time interval, so that he doesn't ends up making multiple calls to the server.

* Calling a function only once in a particular time interval on mouse hover.

Now, let's see some code in action:

```HTML
<button>
Click!
</button>
```

```Javascript
const button = document.querySelector('button');
const getDetails = () => {
	console.log("details");
}
Rx.Observable.fromEvent(button,'click').subscribe(getDetails);
```

Well, the idea behind the above code is pretty simple:

* We are creating an observable from click event on the button.
* Then subscribing to that observable using the the `subscribe` operator and pass `getDetails` function to it.
* In the get details function we are just logging the words "details".

Everytime the button is clicked, the observable emits a value and and due to the subscription on this observable, `getDetails` function is called and word "details" is logged on console.

Now, suppose we want to limit the rate at which word "details" is logged, say once in 2 seconds, we can change our existing code by adding `throttleTime` operator and passing time `2000ms` to it. Here is the modified code:


```Javascript
const button = document.querySelector('button');
const getDetails = () => {
	console.log("details");
}
Rx.Observable.fromEvent(button,'click').throttleTime(2000).subscribe(getDetails);
```

Now, even if you click multiple times on the "Click!" button, word "details" would be logged only once in 2 seconds.

So how does `throttleTime` works?

* Initially the timer is disabled.
* As soon as the first value arrives, it is emitted and timer is enabled.
* Timer remains enabled for the `x` duration passed as a param to the `throttleTime` operator.
* As soon as `x` duration passes, timer is disabled and the process repeats for the next source value.

[Here](https://jsfiddle.net/namita1990/4L9212x7/13/) is a working snippet for this small demo.