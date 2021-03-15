+++
title = "The death of the for loop?"
tags = ["code-quality", "functional"]
date = "2021-03-13"
author = "Braydon Kains"
+++

Generally introduced to new developers around chapter 4 or 5 of their proverbial Intro to Computer Science books, loops are one of the most fundamental coding constructs a developer learns. The different simple ways we iterate over collections of data are often the core of the most complex applications ever built. This is to dramatically justify the probably-overkill rant I am about to write regarding iterating over a collection of data.

Truthfully, this title is a misnomer. I don't think `for` loops need to die. My goal with this post is to present a case for the available alternatives to traditional `for` loops. Though they aren't technically wrong, I hope to demonstrate the benefits of the alternatives and how I believe they contribute to the enhancement of code quality.
(Ha, my first clickbait title. Unfortunately, this site earns me nothing. Your click paid me $0.)

I will use JavaScript for the code examples since types aren't going to be a factor here, and I feel it's the simplest language to convey the concepts in the post. I will stay as language-agnostic as possible.

# The traditional `for` Loop

I'll start by laying out the traditional `for` loop everyone knows and loves. While learning the fundamentals of coding you will write your first `for` loops like this.

```javascript
for (let i = 0; i < 10; i++) {
	// Code to perform on each iteration
}
```

I still think this should be the first loop a new developer learns. It's easy to understand; perform the code inside the braces 10 times. Once the developer gets to arrays, and they learn that arrays are addressed 0, 1, 2 etc. to retrieve data, the use case of `for` loops suddenly clicks:

```javascript
const arr = [1, 2, 3, 4, 5];
for (let i = 0; i < arr.length; i++) {
	console.log(arr[i]);
}
```

"The loop runs from 0 to 4, so I can use `i` to choose an item from the array on each iteration of the loop! Now I understand what loops are for!" - A dramatic re-enactment of me getting to the array lesson in my first coding book, feeling like a genius.  
It's understandable why a developer would reach for this by default; any professional developer is guaranteed to have written hundreds of `for` loops exactly like this throughout their career, so there is rarely anything new for a developer to learn or understand.

Why fix what isn't broken?

# `foreach` loops 

How many times have you seen a loop like this?

```javascript
const arr = [1, 2, 3, 4, 5];
for (let i = 0; i < arr.length; i++) {
	const value = arr[i];
	// do stuff with currentValue
}
```

This code isn't inherently wrong, but doesn't it seem like a waste to write a traditional `for` loop just to assign the value to a local constant variable every time? We don't actually need to modify the source collection, we just need to iterate through it and read each value individually.  
Enter the `foreach` loop. This style of loop cuts out that boilerplate step that assigs a local constant in each iteration of your loop. Instead of each loop iteration having an index, each loop iteration will have an item. In JavaScript, this is implemented using the `for...of` syntax.

```javascript
const arr = [1, 2, 3, 4, 5];
for (const value of arr) {
	// do stuff with value
}
```

In my opinion, this second option is a lot cleaner. Using this new syntax, we keep our code more concise and focused by demonstrating our intentions with the data through the syntax.  
This is a microcosm of what you'll see in the rest of this post; while it's not wrong to use a traditional for loop, we should find ways to be more explicit about our intention when we iterate through a collection.

# Higher-order Functions

Rather than mince words to try and explain what a Higher-order Function (hereby referred to as HOF) is, I will simply link its [Wikipedia Article](https://en.wikipedia.org/wiki/Higher-order_function), as well as this [chapter of Eloquent JavaScript](https://eloquentjavascript.net/05_higher_order.html) since this article is largely in JavaScript.  
Why are HOFs important to the goals of this post? This fundamental construct unlocks a number of elegant ways to use and transform collections of data with more specificity than is generally possible with native looping constructs.  
Assuming that you have read the suggested articles or are already familiar with the required anonymous function syntax, let's look at some HOFs that we can use to work with collections of data. While the examples will still be in JavaScript, nearly every modern language has some version of the methods we'll discuss here.

# `forEach`

We have learned about `foreach` loops, implemented in JavaScript as `for...of`. However, there's a HOF to do essentially the same thing. Let's restate the previous example here:

```javascript
const arr = [1, 2, 3, 4, 5];
for (const value of arr) {
	// do stuff with value
}
```

The `foreach` function takes in an anonymous function with one argument that represents an individual element of the collection. So the above example could be refactored to this:

```javascript
const arr = [1, 2, 3, 4, 5];
arr.forEach(
	value => {
		// do stuff with value
	}
);
```

When I see this function, I think "We are going to perform an operation that reads each element of the array individually" and I can focus on how we will use each element.  
I started with the `forEach` function because it's a great introduction to HOFs in general. However, to someone not sold on HOFs as a concept, this might look no better than a `for...of` loop. In truth, the justification for this goes deeper into the concept of immutability and side effects that are core to the Functional Programming Manifesto. (I capitalized that like it was a real book, but unfortunately it's not. Lots of great reading if you search that exact phrase, though.)

In the interest of staying in scope for this post, let's instead move ahead to some similar HOFs that I believe provide a clear new advantage.

# `map`

`map` is designed to handle the scenario where we want to apply a transformation to every element of an array. For example you may have a loop that wants to build the 2's timestable.

```javascript
const arr = [1, 2, 3, 4, 5];
const twoTimestable = [];
for (const value of arr) {
	twoTimestable.push(value * 2);
}
// twoTimestable = [2, 4, 6, 8, 10]
```

When another coder reads this, they will be able to tell that this is a loop to build a new collection of data based on each element of a source. Using the `map` function, we can instead specify that a new collection is a result of transforming the source's elements individually. 

```javascript
const arr = [1, 2, 3, 4, 5];
const twoTimestable = arr.map(value => value * 2);
// twoTimestable = [2, 4, 6, 8, 10]
```

When I read the second example, I see the `map` function and instantly think "This is a new collection that is a transformation of the original", and I can focus on what exactly the transformation is. That's the important part anyway; the extra code that manages assigning the results to a new collection is simply boilerplate around what I would consider the unique behaviour of the program. It's what makes this program special, if you will.  

# `filter`

`filter` is for when we need specific data out of a collection. Let's say we want an array containing only the elements of our source that are divisible by 3.

```javascript
const arr = [1, 3, 6, 8, 12];
const divisibleBy3 = [];
for (const value of arr) {
	if (value % 3 === 0) {
		divisibleBy3.push(value * 2);
	}
}
// divisibleBy3 = [3, 6, 12]
```

`filter` will give us similar benefits to `map` here. `filter` is a function that will produce a new collection that only contains the elements of source for which the specified function returns `true`. So the above example can be refactored to this:

```javascript
const arr = [1, 3, 6, 8, 12];
const divisibleBy3 = arr.filter(value => value % 3 === 0);
// divisibleBy3 = [3, 6, 12]
```

When I see `filter`, I think "This will be a new collection of data that passes some criteria", and then I can focus on the criteria. As with `map`, that's what makes this program special.  
In my eyes, this seems like the easiest HOF to sell. It is in my opinion the most intuitive because it's the word we would probably use in plain English to describe what we are actually trying to do.

# `reduce` (traditionally known as `fold`)

This one may be the hardest to sell of the 4 HOFs we're exploring.  
`reduce` is used for when we want to take the elements of a collection and deduce some final result from it. A good basic example would be calculating the sum of all elements in an integer array.

```javascript
const arr = [1, 2, 3, 4, 5];
const sum = 0;
for (const value of arr) {
	sum += value;
}
// sum = 15 
```

While the idea of `reduce` is simple in explanation, its usage is a bit harder to wrap your head around at first. While the previous HOFs have accepted an anonymous function with a single argument (which represents the "current element" so to speak), the anonymous function we pass into `reduce` requires 2: the running value known as the "accumulator", and the current element (like the previous examples). This function will then return the new value for the accumulator after whatever action for the current element. In this example, the "accumulator" will be the sum we're calculating. We'll seed the accumulator with some value (in this case 0) as the second argument to the outer `reduce` function.

```javascript
const arr = [1, 2, 3, 4, 5];
const sum = arr.reduce(
	(sum, value) => sum + value,
	0
);
// sum = 15 
```

Building a result that combines all the elements in a collection is a great use of `reduce`, but it can also be good for finding an element in a collection based on some criteria relative to the other elements in a collection. For example, if we wanted to find the max element in an array with a traditional for loop it would look something like this:

```javascript
const arr = [1, 2, 5, 4, 3];
const max = Number.MIN_VALUE;
for (const value of arr) {
	if (value > max) {
		max = value;
	}
}
// max = 5
```

The name "accumulator" becomes a slight misnomer in this scenario, because rather than being an accumulation of all the values, it is simply the end result we are interested in. Ignoring that, the `reduce` we right is pretty similar to the earlier example:

```javascript
const arr = [1, 2, 5, 4, 3];
const max = arr.reduce(
	(max, value) => {
		if (value > max) {
			return value;
		}
		return max;
	}
	Number.MIN_VALUE
);
// max = 5 
```

You might be thinking "you silly goose, this is more lines than the original `for`!"  
Correct, I have bamboozled you to demonstrate a common gotcha for writing these HOF argument functions; they do need to return a value. The way we've been writing them (without braces) implies that the calculation is the return value of the function. However if you go to write your first reduce and wonder why the heck it's not working, the first check is to ensure that all code paths are returning a value.  
This example can be written as a one-liner using a ternary expression:

```javascript
const arr = [1, 2, 5, 4, 3];
const max = arr.reduce(
	(max, value) => value > max ? value : max,
	Number.MIN_VALUE
);
// max = 5 
```

When I see a `reduce`, I think "This will take the source collection and build some kind of result out of it", and I can focus on what it needs to do to find that result.

# This is so sad, I love `for` loops. There must be some use for them!

Fear not! HOFs are awesome, and in a pure functional language like Haskell, you would only be using them at all times. However, if you are not living in the Pure Functional Utopia, there are some still some great uses for traditional `for` loops.

# Modifying the source collection

This post has so far assumed that you are only *reading* from the source collection and producing a new result. I always strive not to modify values in code; it's so nice to always know with certainty what everything in your program is going to contain/equal. However, if for whatever reason you are required to modify a collection in place, a traditional index `for` loop is still the best way to cleanly do so. 

```javascript
const arr = [1, 2, 3];
for (let i = 0; i < arr.length; i++) {
	arr[i] = 69;
}
// arr = [69, 69, 69];
```

# Comparing elements directly to previous or future elements

If you are in a scenario where you need to take different action based on elements before/after the current element, the traditional index `for` loop is going to be your best bet. 

```javascript
const arr = [1, 1, 1, 2, 2, 3];
for (let i = 0; i < arr.length; i++) {
	if (arr[i] == arr[i - 1]) {
		// do something 
	} else {
		// do something different 
	}
}
```

# You are writing Go

Let's take a brief intermission from JavaScript. To present a new perspective.  
If you're writing Go, you can probably throw everything I've said in this post out the window. The way Go handles loops is essentially the antithesis to what I've presented so far.  
Not only is a `for` loop the only way to iterate through a collection of data, the `for` keyword even replaced the `while` keyword. This is in service of Go's language design philosophy, which is (in brief) to standardize around one way to do things as much as possible (a philosophy I'd love to rant on in a future post).  
Go does have a `func` type, meaning one could implement their own `map` function like so:

```go
func Map(arr []int, transform func(int) int) []int {
	result := make([]int, len(arr))
	for i, value := range arr {
		result[i] = transform(value)
	}
	return result
}

func main() {
	arr := []int{1, 2, 3, 4, 5}
	twoTimesTable := Map(
		arr,
		func(value int) int { return value * 2 },
	)
	// twoTimesTable = [2, 4, 6, 8, 10]
}
```
(I wrote this myself, but would not have been able to without [this post](https://yourbasic.org/golang/function-pointer-type-declaration/) from Algorithms to Go.)

The beauty and curse of Go is that it's up the developer to implement this if they want it. I don't know any Go developers personally, but I imagine a some would turn their nose at this while others would welcome it. If you happen to be a Go developer, I would love to hear your thoughts on this!

# You are writing C

Just use `for` loops.

# Conclusion

You may read this post and think to yourself "this guy is stupid, it's more readable to just use a for loop".  
You might be right given the context of the code you're writing. "Readibility" is pretty subjective, and some people may prefer to see the boilerplate that comes with the `for` loop examples I presented here. My goal with this post was to present my perspective; I love the way HOFs describe explicit intentions for an iteration through a collection, allowing me to focus on the part of the code that matters. 

I hope that you enjoyed reading this post! This is my first public blog post and I'm pretty nervous to show it to the world, but I really hope you gained something out of it and did not leave in an unbridled rage. Please follow my socials if you enjoyed this post and want to read more of my ramblings!
