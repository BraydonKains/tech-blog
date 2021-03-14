+++
title = "The death of the for loop?"
tags = ["code-quality", "functional"]
date = "2021-03-13"
author = "Braydon Kains"
+++

Generally introduced to new developers around chapter 4 or 5 of their proverbial Intro to Computer Science books, loops are one of the most fundamental coding constructs a developer learns. The different simple ways we iterate over collections of data are often the core of the most complex applications ever built. This is to dramatically justify the probably-overkill rant I am about to write regarding loops.

I'll give you my final verdict here early, and you can decide if you can read on without a boiling rage:
The standard `for` loop is so fundamental that it tends to be a coder's first instinct when given a set of data. However, modern languages provide a number of more sophisticated ways to iterate over a set of data. The standard `for` loop shouldn't be forgotten, but it should instead be used when it is truly the right tool for the job.
So the title is a bit of a misnomer right off the bat. I'm not a hardcore enough Functional Programming Intellectual to suggest `for` loops be completely removed from a developer's toolkit, but in this post I will lay out some of the other options available before talking about where I feel traditional `for` loops are best used.  
The goal in this post will be to demonstrate other ways we can work with a collection of data that more clearly exhibits our intentions in our code.

I will use JavaScript for the code examples since types aren't going to be a factor here, and I feel it's the best language to convey the thesis of the post.

# The traditional `for` Loop

I'll start by laying out the traditional `for` loop that everyone knows and loves. While learning coding fundamentals you will start out writing `for` loops like this.

```javascript
for (let i = 0; i < 10; i++) {
	// Code to perform on each iteration
}
```

I'm of the opinion that this is the almost always the first loop a new developer should learn. It's easy to understand; perform the code inside the braces 10 times. Once the developer gets to arrays, and they learn that arrays are addressed 0, 1, 2 etc. to get data, the use of `for` loops suddenly clicks:

```javascript
const arr = [1, 2, 3, 4, 5];
for (let i = 0; i < arr.length; i++) {
	console.log(arr[i]);
}
```

"The loop runs from 0 to 4, allowing us to use `i` to choose an item for the array on each iteration of the loop! Amazing, I understand what loops are for now!" - A dramatic re-enactment of getting to the array lesson in my first coding book, feeling like a genius.  
It's simple and easy to understand. It's understandable why a developer would always use this; any professional developer is guaranteed to have written hundreds of for loops exactly like this in their career, and there will be no ambiguity about what's going on here.

Why fix what isn't broken?

# Why `foreach` loops exist

How many times have you seen a loop like this?

```javascript
const arr = [1, 2, 3, 4, 5];
for (let i = 0; i < arr.length; i++) {
	const value = arr[i];
	// do stuff with currentValue
}
```

This code isn't inherently wrong, but doesn't it seem like a waste to write a traditional index `for` loop just to assign the value to a local variable every time? We don't actually need to modify the source collection, we just need to iterate through it and read each value.  
Enter the `foreach` loop. This is a style of loop that cuts out the middle step of always assigning a local variable in each iteration of your loop. Instead of a loop where each iteration has an index, you have a loop where each iteration is an item. In JavaScript, this is implemented using the `for...of` syntax.

```javascript
const arr = [1, 2, 3, 4, 5];
for (const value of arr) {
	// do stuff with value
}
```

In my opinion, this is a big improvement. While the first example wasn't technically wrong, the second option is a lot cleaner. This is a microcosm of what you'll see in the rest of this post; while it's not technically wrong to use a traditional for loop, we should find ways to be more specific about our intention when we iterate.

# Higher-order Functions

Rather than mince words to try and explain what a Higher-order Function (HOF) is, I will simply link the [Wikipedia Article](https://en.wikipedia.org/wiki/Higher-order_function) on it, as well as this [chapter of Eloquent JavaScript](https://eloquentjavascript.net/05_higher_order.html) since this article is largely in JavaScript.  
Why are Higher-order Functions important to the topic of this post? This fundamental construct unlocks a lot of elegant ways to use and transform collections of data with a higher level of specificity than is generally possible with native looping constructs.  
Now assuming that you have read the suggested articles or are already familiar with the required syntax, let's look at some HOFs that we can use to work with collections of data. While the examples will still be in JavaScript, nearly every modern language has some version of the methods we'll discuss here.

# `forEach`

We have learned about `foreach` loops, implemented in JavaScript as `for...of`. However, there's also a HOF to do something very similar. So a loop like this:

```javascript
const arr = [1, 2, 3, 4, 5];
for (const value of arr) {
	// do stuff with value
}
```

Could instead be refactored to this:

```javascript
const arr = [1, 2, 3, 4, 5];
arr.forEach(
	value => {
		// do stuff with value
	}
);
```

To someone who is not sold on HOFs as a concept, this might look no better than a `for...of` loop. I think the discussion of why this would be an improvement is diving too deep into the manifesto of functional programming. Instead let's move ahead to some HOFs that provide far more obvious value over traditional loops.

# `map`

`map` is designed to handle the scenarios where we actually want to transform every element of an array in the same way. For example you may have a loop that wants to build the 2's timestable.

```javascript
const arr = [1, 2, 3, 4, 5];
const twoTimestable = [];
for (const value of arr) {
	twoTimestable.push(value * 2);
}
// twoTimestable = [2, 4, 6, 8, 10]
```

This is not wrong, but we can be more explicit. When another person reads this, they will be able to tell that this is a loop which builds a new collection of data based on the elements of a source. However using the `map` function, we can instead specify that this new collection is a result of transforming the source's elements individually. 

```javascript
const arr = [1, 2, 3, 4, 5];
const twoTimestable = arr.map(value => value * 2);
// twoTimestable = [2, 4, 6, 8, 10]
```

When I read the second example, I see the `map` function and instantly think "Okay, this is a new collection that is a transformation of the original", and I can instead focus on what exactly the transformation is. That's the important part anyway; the extra code that manages assigning the results to a new collection is simply boilerplate around the actual behaviour of the program.  
We will see this theme continue as we look at more HOFs.

# `filter`

`filter` is for those times when you need specific data out of a collection. Let's say we want an array containing only the elements of our source that are divisible by 3.

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

Again, this is easy enough to understand. However, `filter` will give us similar benefits to `map` here. `filter` will produce a new collection that only contains the elements of source for which the argument-function returns `true`. So the above example would look like this:

```javascript
const arr = [1, 3, 6, 8, 12];
const divisibleBy3 = arr.filter(value => value % 3 === 0);
// divisibleBy3 = [3, 6, 12]
```

When I see `filter`, I think "This will be a collection of data that passes some criteria", and then I can focus on the criteria.  
In my eyes, this seems like the easiest HOF to sell. It is in my opinion to most intuitive, and I find it plainly superior to the traditional `loop` alternative.  
The last HOF we'll look at may be the hardest sell.

# `reduce` (traditionally known as `fold`)

`reduce` is used for the scenarios when you want to take the elements of a collection and discover some single final result from it. A very common example would be calculating the sum of all elements in an integer array.

```javascript
const arr = [1, 2, 3, 4, 5];
const sum = 0;
for (const value of arr) {
	sum += value;
}
// sum = 15 
```

While the idea of `reduce` is I think pretty simple, its usage is a bit harder to wrap your head around at first. While the previous HOFs have accepted a function with a single argument (which represents the "current element" so to speak), the function we pass into `reduce` requires 2: the running value known as the "accumulator", and the current element similarly to the previous examples. The function will then return the new value for the accumulator after whatever transformation for the current element. In this example, the "accumulator" will be the sum that we are calculating. We will seed the accumulator with some value (in this case 0) as the second argument to the outer `reduce` function.

```javascript
const arr = [1, 2, 3, 4, 5];
const sum = arr.reduce(
	(sum, value) => sum + value,
	0
);
// sum = 15 
```

This concept of building a result out of all the elements in a collection is a great use of reduce, but it can also be good for finding an element in a collection based on some criteria relative to the others in a collection. For example, if we wanted to find the max element in an array with a traditional for loop it would look like this:

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

The name "accumulator" becomes a slight misnomer in this scenario, because rather than being an accumulation of all the values, it is simply the result we are interested in. Ignoring that, the `reduce` we right is pretty similar to the earlier example:

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

You might be thinking "you silly goose, this is more lines than the `for` loop way!"  
I have bamboozled you to demonstrate a common gotcha for writing these HOF argument methods; they do have to return a value. The way we have been writing them (without braces) implies that the calculation is the return value of the method. However if you go to write your first reduce and wonder why the heck it's not working, check to ensure that all code paths are returning a value.  
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

Fear not! HOFs are awesome, and in a pure functional language like Haskell, you would only be using them at all times. However, if you are not living in the Pure Functional Utopia, there are some still some great uses of traditional `for` loops.

# Modifying the source collection

This post has so far assumed that you are only *reading* from the source collection. I always strive not to modify values in code; it's so nice to always know with certainty what data in your program is going to contain/equal. However, if for whatever reason you are required to modify a collection in place, a traditional index `for` loop is really the only way to cleanly do so. 

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

Let's take a brief intermission from JavaScript. To present an alternative perspective.  
Go basically designed the antithesis to the direction I have suggested throughout this post. Not only is a `for` loop the only way to iterate through a collection of data, the `for` keyword even replaced the `while` keyword. This is in service of Go's design philosophy, which is (in brief) to standardize around one way to do things as much as possible (a topic I'd love to rant on in a future post).  
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
	// arr = [2, 4, 6, 8, 10]
}
```

The beauty and curse of Go is that it's up to the individual developer to implement this if they want it. I don't know any Go developers personally, but I imagine a lot of some would turn their nose at this while others would welcome it. If you happen to be a Go developer, I would love to hear your thoughts on whether this is something you'd do in your own code and why or why not!

# You are writing C

Just use `for` loops.

# Conclusion

You may read this post and think to yourself "this guy is stupid, it's more readable to just use a for loop".  
It is not technically wrong to use a `for` loop in the scenarios I presented above. My goal with this article was simply to present alternative, more explicit methods of iterating over data. Code readability is pretty subjective, but I know I personally feel like I understand the high level intentions of the original author more when the HOFs are used than the different ways of using the same loop construct. I hope that this article was able to open your mind in some way, and perhaps have you considering some refactors if you like what you saw.
