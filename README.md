# How does Wordle work?

## From source bundle

We can use the dev tools in our browser to get the javascript bundle. By searching for the term "solution" in the js bundle in the dev tools we can find the first function called and from there we work our way around all the function called to find the solution. (Ma is an array of all the words that can be a valid solution -> too long to paste it here).

This are the functions of interset (written in a bit more modern syntax):

```ts
(e.today = new Date()), (e.solution = Va(e.today));
var Ba = new Date(2021, 5, 19, 0, 0, 0, 0);

function Va(e) {
	var a,
		s = Fa(e);
	return (a = s % Ma.length), Ma[a];
}

function Fa(e) {
	return Ga(Ba, e);
}

function Ga(e, a) {
	var s = new Date(e),
		t = new Date(a).setHours(0, 0, 0, 0) - s.setHours(0, 0, 0, 0);
	return Math.round(t / 864e5);
}
```

## Translate the bundle code

Let us write the functions in a more readable way:

```ts
const solution = Va(new Date());

const Va = (today) => {
	const s = Fa(today);
	const a = s % Ma.length;
	return Ma[a];
};
const Ba = new Date(2021, 5, 19, 0, 0, 0, 0);

const Fa = (today) => {
	return Ga(Ba, today);
};

const Ga = (Ba, today) => {
	const s = new Date(Ba);
	const t = new Date(today).setHours(0, 0, 0, 0) - s.setHours(0, 0, 0, 0);
	return Math.round(t / 864e5);
};
```

## Code refactoring

Now we can use some more readable names and compress all the code in 2 functions.

```ts
const getWordKey = (keyDate, today) => {
	const keyDateClone = new Date(keyDate);
	const todayResetted = new Date(today).setHours(0, 0, 0, 0);
	const keyDateResetted = new Date(keyDateClone).setHours(0, 0, 0, 0);

	const diff = todayResetted - keyDateResetted;

	return Math.round(diff / 864e5);
};

const getWordleSolution = (keyDate = new Date(2021, 5, 19, 0, 0, 0, 0)) => {
	const today = new Date();
	const index = getWordKey(keyDate, today);
	const boundedIndex = index % WORDS.length;
	return WORDS[boundedIndex];
};
```

## How it actually work

It is pretty easy to understand how it works now:

We take the date of today and a key date, we reset the hours, minutes and milliseconds of both (to get the same key for 24 hours -> same word for 24h).
Afterwards we get the difference between the 2 dates (unique value for each day) and we divide by a constant (to get a smaller result in the order of the words array length).
Once we got the unique key we just need to find the index in the words array: we do this by using the modulo operator (which returns a value between 0 and `words.length - 1`)

[Wordle clone to understand better how it works](https://github.com/cwackerfuss/react-wordle/blob/main/src/lib/words.ts)
