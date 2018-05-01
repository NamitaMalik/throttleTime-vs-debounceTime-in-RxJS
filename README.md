# throttleTime vs debounceTime in RxJS

We all know that the Reactive Extensions for JavaScript (RxJS) is a library for composing asynchronous and event-based programs. RxJS comes with wide range of operators and in this blog we will discuss about `throttleTime` and `debounceTime` operators.

Before I move to `throttleTime`, lets understand what `throttling` means. So in simple terms `throttling` means to control the rate at which a process is conducted. So if you have situation when you want to start a process, then wait for `x` time and then resume and repeat the process, you would need to `throttle` that process.

Coming to an application level use case, suppose there is a situation when you want to abstain a user from continuously firing events, `throttleTime` can be helpful in such situations. A couple of such scenarios can be :

* Allowing a user to click a button only once per time interval, so that he doesn't ends up making multiple calls to the server.

* Calling a function only once in a particular time interval on mouse hover.

Now, let's see some code in action:

```HTML
<button>Click!</button>
```

```Javascript
let counter = 0;
const button = document.querySelector('button');
const incrementCounter = () => {
	console.log(counter++);
}
Rx.Observable.fromEvent(button,'click').subscribe(incrementCounter);
```

Well, the idea behind the above code is pretty simple:

* We are creating an observable from click event on the button.
* Then subscribing to that observable using the the `subscribe` operator and pass `incrementCounter` function to it.
* In the `incrementCounter` function we are incrementing the counter and logging it on the console.

Every time the button is clicked, the observable emits a value and and due to the subscription on this observable, `incrementCounter` function is called and value of the counter is logged on console.

Now, suppose we want to limit the rate at which counter can be incremented, say once in 2 seconds, we can change our existing code by adding `throttleTime` operator and passing time `2000ms` to it. Here is the modified code:


```Javascript
let counter = 0;
const button = document.querySelector('button');
const incrementCounter = () => {
	console.log(counter++);
}
Rx.Observable.fromEvent(button,'click').throttleTime(2000).subscribe(incrementCounter);
```

*throttleTime in action*

![throttleTime on click](https://raw.githubusercontent.com/NamitaMalik/throttleTime-vs-debounceTime-in-RxJS/master/throttleTime_on_click.gif)


So when first time you click the button, `0` would be printed and then even if you click the button again multiple times, `1` would be printed only after 2 seconds.

This means even if you click multiple times on the "Click!" button, counter would be incremented and logged only once in 2 seconds.

So how does `throttleTime` works?

* Initially the timer is disabled.
* As soon as the first value arrives, it is emitted and timer is enabled.
* Timer remains enabled for the `x` duration passed as a param to the `throttleTime` operator.
* As soon as `x` duration passes, timer is disabled and the process repeats for the next source value.

[Here](https://jsfiddle.net/namita1990/4L9212x7/23/) is a working demo.

Now, let's move on to `debouncingTime` operator. But before we move, lets understand what does the term `debounce` means. Well, the term `debouncing` is mostly related to hardware, electrical switches, micro-controllers etc. It is basically a way for eliminating unwanted signals from an input.

A **typeahead** / **autocomplete** is the classic use case for debouncing. Now suppose user is typing something we would want to make an API call, to show the suggestions according to the input value entered by the user. But it would be better to make API call in a controlled manner, otherwise we will end up making numerous un-needed calls to the server. 

`debounceTime` is similar to `throttleTime` except one key difference is that this operator keeps track of the most recent value from the Observable, and emits that only when the defined duration has passed without any other value appearing on the source Observable.

Let's have a look at the below code:

```HTML
<input type="text">
```

```Javascript
const input = document.querySelector('input');
const printInput = (userInput) => {
	console.log(userInput.target.value);
}
Rx.Observable.fromEvent(input,'keyup').subscribe(printInput);
```

If you run the above code and type anything in the input box, it would be logged on console. But we don't want to do that. We want to print the user input in a more controlled manner, so that not all values are printed.
We just need to add `debounceTime` operator to the above code.

So our code would look like:

```Javascript
const input = document.querySelector('input');
const printInput = (userInput) => {
	console.log(userInput.target.value);
 }
 Rx.Observable.fromEvent(input,'keyup').debounceTime(1000).subscribe(printInput);
 ```

[Here](https://jsfiddle.net/namita1990/ynL6hhh0/) is working demo.

So how does `debounceTime` works?

* Keeps track of the most recent value from the source Observable.
* Once dueTime has passed without any other value appearing on the source Observable, that value is emitted.
* If a new value appears, then that new value will be emitted to the output Observable and old value will be dropped.

You, would now be thinking we could have used `throttleTime` here instead of `debounceTime`. Have a look at the below
images, as they might help clear the confusion:

*throttleTime on input*

![throttleTime](https://raw.githubusercontent.com/NamitaMalik/throttleTime-vs-debounceTime-in-RxJS/master/throttleTime.gif)

We can notice here that value was emitted once in a second. This was not our intention,
as we need the most recent value emitted by the observable in this case.

*debounceTime on input*

![debounceTime](https://raw.githubusercontent.com/NamitaMalik/throttleTime-vs-debounceTime-in-RxJS/master/debunceTime.gif)

In the above gif you would have noticed that most recent value is emitted
and the older value is dropped, which was the intention.

So next time when we have to restrict multiple clicks at a time on a button
or have to fetch data on the basis of keyed in value, we know which operators
to use!

Follow Me
---
[Github](https://github.com/NamitaMalik)

[Twitter](https://twitter.com/namita13_04)

[LinkedIn](https://in.linkedin.com/in/namita-malik-a7885b23)

[More Blogs By Me](https://namitamalik.github.io/)