# Leverage tail recursion for faster runtimes and a smaller footprint

Recursion is a programming concept you probably already know from studying data structures and algorithms. It's a highly readable, easily understandable way of writing methods that involves a call to itself within its definition. However, iterative code that utilizes loops and shared memory is often more efficient in both memory and CPU usage. As StackOverflow user [Leigh Caldwell](https://stackoverflow.com/users/3267/leigh-caldwell) puts it,

> Loops may achieve a performance gain for your program. Recursion may achieve a performance gain for your programmer. Choose which is more important in your situation!

Fortunately, a method of programming called **tail recursion** combines these concepts to drastically improve the efficiency of your code while remaining easily readable and maintainable. Let's dive into tail recursion, more about what it is, how to use it, and why you should be using it over traditional recursion.

## So what is tail recursion?

A tail recursive function is one where the recursive function call is the last operation it executes. Let's look at a classic recursive problem: finding the sum of a list of numbers.

First, let's look at how you might intuitively solve this problem using traditional recursion:

### Traditional Recursion

```python
def sum(array):
    if array == []:
	return 0
    else:
	return array[0] + sum(array[1:])
```

Consider how the computer executes this code when the function call is `sum([1, 2, 3, 4, 5])`:

```python
sum([1, 2, 3, 4, 5])
1 + sum([2, 3, 4, 5])
1 + 2 + sum([3, 4, 5])
1 + 2 + 3 + sum([4, 5])
1 + 2 + 3 + 4 + sum([5])
1 + 2 + 3 + 4 + 5 + sum([])
1 + 2 + 3 + 4 + 5 + 0
1 + 2 + 3 + 4 + 5
1 + 2 + 3 + 9
1 + 2 + 12
1 + 14
15
```

That was a lot of work to get our answer! Notice how the computer didn't start computing our sum until every recursive call was completed? That's not ideal. Let's take a look at how tail recursion can fix that problem:

### Tail Reucrsion: The Better Approach

```python
# runningSum defaults to 0 if only one argument is given
def sum(array, runningSum = 0): 
    if array == []:
	return runningSum
    else:
	return sum(array[1:], runningSum + array[0])
```

Here, our execution trace looks more like this:

```python
sum([1, 2, 3, 4, 5])
sum([2, 3, 4, 5], 1)
sum([3, 4, 5], 3)
sum([4, 5], 6)
sum([5], 10)
sum([], 15)
15
```

A lot smaller, right? Our computer will think so too, consuming much less memory and performing the computations as we recurse rather than all at once at the end. When working with large datasets, this approach can save you a lot of runtime and stay within reasonable bounds for memory usage.

## How to convert a recursive function to tail recursion?

Now that you're sold, you can convert a recursive function to tail recursion in one of two ways. The most common method is to use an **accumulator,** like our `runningSum` from the previous example. Alternatively, you can use a technique called **continuation-passing style** which we'll explore in a moment.

### Using Accumulators

An accumulator is a variable that keeps track of the intermediate result of the computation. Instead of returning the result of each recursive call, we pass the accumulated result as an argument to the next call.

For example, consider the following recursive function to calculate the factorial of a number:

```python
def factorial(n):
    if n == 0:
        return 1
    else:
        return n * factorial(n - 1)
```

We can convert this function to tail-recursive by using an accumulator, called `acc` in this case:

```python
def factorial(n, acc=1):
    if n == 0:
        return acc
    else:
        return factorial(n - 1, n * acc)
```

Here, our argument `acc` keeps track of the intermediate result. In each recursive call, we multiply `n` by `acc` and pass it as the new value of `acc`. The final result is returned when n becomes 0, and we have nothing left to recurse on. In this way, computations are performed each time we call the function, rather than delaying, and memory usage is kept constant.

### Using CPS (Continuation-Passing Style)

An alternative method to convert a recursive function to tail-recursive is by
using Continuation-Passing Style (CPS). Here, instead of returning the
 result of a function, we pass a "continuation function" as an argument, which is a function that represents the rest of the
computation that needs to be performed with the result of the current
function.

Consider our factorial example once more:

```python
def factorial_cps(n, cont):
    if n == 0:
        return cont(1)
    else:
        return factorial_cps(n - 1, lambda res: cont(n * res))
```

Here, we added an extra argument `cont`, our continuation function. In each recursive call, we pass into it a lambda function that multiplies n with the result obtained from the previous call. The final result is obtained by calling the continuation function with the accumulated value of the multiplication.

### Which to use?

As with everything nice like this, there's not a clear answer. Some problems may not work using an accumulator variable, whereas others will. Ultimately, when possible, an accumulator is *preferred* because it's easier to read and implement. But, when the problem requires (example: depth of a binary tree) don't be afraid to implement a continuation-passing style function callback that gets the job done efficiently.

## Thanks for Reading!

If you enjoyed this write-up, you'll love everything else we offer over at [devbranch.co](https://devbranch.co)! We cover more awesome topics like this one, we have a [newsletter](https://devbranch.co/newsletter) to keep you constantly informed, and we regularly post exclusive discounts on things like hosting, domain names, and tech.

### Contact the Author

[![Twitter](https://img.shields.io/badge/Twitter-%231DA1F2.svg?style=for-the-badge&logo=Twitter&logoColor=white)](https://twitter.com/peterauscher)
[![GitHub](https://img.shields.io/badge/github-%23121011.svg?style=for-the-badge&logo=github&logoColor=white)](https://github.com/peterrauscher)
[![LinkedIn](https://img.shields.io/badge/linkedin-%230077B5.svg?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/peter-rauscher)
[![Protonmail](https://img.shields.io/badge/ProtonMail-8B89CC?style=for-the-badge&logo=protonmail&logoColor=white)](mailto:peterrauscher@protonmail.com)
