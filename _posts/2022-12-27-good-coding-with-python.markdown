---
layout: post
title:  Good Coding with Python from the Very Beginning
permalink: /good-coding-with-python/
date:   2022-12-27 17:28:00 +0800
categories: Python
---
I'm not an extremely good programmer, but sometimes I manage to throw dust in people's eyes, and they start thinking that I'm a senior developer. And over time, it somehow happened that I started to perform a lot of code reviews. Looking through file after file, one day I saw that people and projects change, but what remains the same are the points that I constantly criticize. So, I've decided to collect the most common patterns in this chaotic article. Hope they will help you write cleaner and more efficient Python code.

## Quitting too early
This point is definitely in the first place, because I see such things in everyone's code:

{% highlight python %}
def foo(a: bool):

    if a:
        #
        # ... 50 LOCs
        #
        return True
        
    else:  # if not a
        return False
{% endhighlight %}

Writing or reading "if a:" sentences, we remember that somewhere closer to the end we should insert the "else:" case. But if the code snippet inside the "if a:" is large enough, then the code in "else:" will be completely outside the context.

What we could do is to swap the conditions:

{% highlight python %}
def foo(a: bool):

    if not a:
        return False

    else:
        # 
        # ... 50 LOCs
        #
        return True
{% endhighlight %}

Here we have a much better readability, since we wrote the "if not a:" sentence at the start and can threw it out of our heads. But after a more detailed review we'll see, that we don't need the "else" at all:

{% highlight python %}
def foo(a: bool):

    if not a:
        return False
        
    # the "not a" is already handled and we can forget about it
    
    # 
    # ... 50 LOCs
    #
    return True
{% endhighlight %}

Here is the trick – when writing a function, we usually try to get out of it as quickly as possible by cutting off some bad cases. This technique is great because it allows you to get rid of nesting levels (that is, the "else" statements), and even unload the programmer's memory by removing some branches of logic.

An artificial example where we see a kind of parsing:

{% highlight python %}
import requests

def scrape(url: str) -> dict:

    response = requests.get(url)
    
    if not response.ok:  # getting rid of "bad" statuses
        return {}
    # starting from this line we know that the response has a "good" status
    
    data = response.json()
    
    if 'error' in data:  # getting rid of responses with errors
        raise ValueError(f'Bad response: {data["error"]}')
    # starting from this line we know that there are no errors
    
    if 'result' not in data:  # getting rid of empty responses
        return {}
    # starting from this line we know that the response is not empty
    
    # ... parse data['result'] ...
{% endhighlight %}

The function is totally "linear", and at a moment of data parsing, we do not have to keep the information in our heads and know for sure: *everything is fine*, the data is in place, there were no errors.

The principle works similarly for loops, but instead of return there will be break or continue.

## Assignment in one line
Let's talk about the assignment of values to variables. Quite often I see the following:

{% highlight python %}
if a:
    v = 1
else:
    v = 2
{% endhighlight %}

Everything is pretty obvious here – why do we need these extra "if-else" statements, if everything can be done in one line:

{% highlight python %}
v = 1 if a else 2
{% endhighlight %}

And what if we do this?

{% highlight python %}
if a == 1:
    v = 10
elif a == 0:
    v = 0
elif a == -1:
    v = -10
elif a == -2:
    v = -20
{% endhighlight %}

Here you can ask: what will happen if a not in {0, 1, -1, -2}. And you guess it right – in this case the v won't be defined at all. Also, if I later need to figure out where the variable v came from and what is written in it, I will need to look in four places because the v is defined 4 times. And this is terrible, because the consequences of declaring a variable many times look like this:

1. you can simply forget to consider some cases
2. you can make a typo and write b instead of v – Python will have absolutely nothing against it
3. you can make a trivial typo when copy-pasting (if you copy sentences and replace their values), and assign the same value in different cases

What should we do? Try to define variables only *once*. In ideal cases any variable declaration should look like this:

{% highlight js %}
var = <some code>
{% endhighlight %}

And that's all. I deliberately write about "ideal cases", because it is not always possible, and sometimes the code readability can suffer. So you need to do everything with caution.

In the case above, I would replace the code with this:

{% highlight python %}
v = {
    1: 10,
    0: 0,
    -1: -10,
    -2: -20,
}[a]
{% endhighlight %}

Here the variable v is defined just once, we can clearly see to what value v each value a leads to, and if a is outside the range of valid values, then everything will fall. And this is good. If you need more default values, then instead of [a] use .get(a, default_value).

## Define variables closer to their usage
It's one more way to unload a programmer's memory. Such code is also quite common:

{% highlight python %}
def foo(url: str):
    
    fields = ['a', 'b', 'c']
    
    response = requests.get(url)
    response.raise_for_status()
    data = response.json()
    if 'result' not in data:
        raise ValueError()
        
    for field in fields:
        print(data[field])
{% endhighlight %}

There is no error, everything is fine. However, when looking at the "for field in fields" line I have *already* forgotten what is in the fields variable, and I have to navigate to the beginning of the function to find its definition there. Yes, in PyCharm you only need to press 2 shortcuts – the first is "jump to definition" and the second is "jump where I was before", but it would be nice not to jump anywhere at all.

And the pattern in question was invented specifically to deal with this problem: we define variables as close as possible to the place where we'll use them. Every moment you want to create a variable, ask yourself: "Do I need this variable in the next code snippet"? If the answer is no, then perhaps you should define it later. Thanks to this, when analyzing the code, you can look at *adjacent* lines and understand where the variables come from and what they contain.

In the example above, we simply move the fields to the place where they are used, and we can even insert them directly into the for loop:

{% highlight python %}
for field in ['a', 'b', 'c']:
    print(data[field])
{% endhighlight %}

## Too many indents
One of the positive features of Python is mandatory indentation. Indentation is great because it shows you the level of nesting in your logic. The more indentation, the more complex the logic, and, accordingly, the more difficult it is for your brain to understand the code and save the stack of conditions.

{% highlight python %}
for x in range(10):
    result = foo(x)
    if result:
        second = bar(result)
        if second:
            print(second)
        else:
            # <<< HERE
            print('not second')
    else:
        print('not good')
{% endhighlight %}

In this example, you need to remember that in the line <<< HERE we have x in the range from 0 to 9, the result is set to True, and the second is False.

There is no certain boundary, like if you have more than N indents, then everything is bad, and if you have less, then it's good. However, the fewer of them, the better. To remove indents you can use the already mentioned "Early Quit" method or move some parts of your code into a separate function.

{% highlight python %}
for x in range(10):
    result = foo(x)
    if not result:
        print('not good')
        continue
    
    second = bar(result)
    print_second(second)
{% endhighlight %}

Special cases of it are double, triple loops, etc., like this

{% highlight python %}
for x in range(100):
    for y in range(100):
        for z in range(100):
            if x % 10 == 0:
                if y % 10 == 0:
                    print('haha')
{% endhighlight %}

Such things can often be simplified using itertools and some functions, for example

{% highlight python %}
from itertools import product

def print_(x: int, y: int, z: int):
    if x % 10 == 0 and y % 10 == 0:
        print('haha')

for x, y, z in product(range(100), range(100), range(100)):
    print_(x, y, z)
{% endhighlight %}

## Danger of loops
By the way, speaking of loops! Ideally, this would be a one level nested loop using some sort of generator:

{% highlight python %}
for i in range(100):
    print(i)
{% endhighlight %}

In real life, things are much more complicated, but you should remember two simple rules that help you not shoot at your feet. The first is the while True loop:

{% highlight python %}
a = 0
while True:
    a += 1
    if a == 100:
        break
{% endhighlight %}

If you change a = 0 to a = 100 for some reason, the loop will become infinite. Someday you'll simply forget to write the exit condition, and you can also write it in such a way that it either won't be executed at all, or will be *unstable*. An infinitely executing program is the last thing you want, and with the while True construct you have a very high chance of getting that.

So just use something that is guaranteed to be *finished*:

{% highlight python %}
max_iterations = 100
a = 0
for i in range(max_iterations):
    a += 1
    if a == 100:
        break
        
else:
    raise ValueError('max_iterations reached')
{% endhighlight %}

The second dangerous category is nested loops. These are easy to write, but with every nested loop you get a terrible increase in complexity. A 20-item loop nested within a 20-item loop gives 400 combinations and could easily create a bottleneck in your program.

{% highlight python %}
for x in range(20):
    for y in range(20):
        fn(x, y)  # called 400 times
{% endhighlight %}

The problem is solved in each case differently, but, for example, you could exclude some cases from processing where possible:

{% highlight python %}
for x, y in product(range(20), range(20)):
    if 10 < x < 20 and 10 < y < 20:
        fn(x, y)
{% endhighlight %}

## Copy-pasting more than two times in a row
The practice of copy-pasting is bad in itself. Firstly, it's hard to read – a bunch of identical code fragments takes up a lot of space. Secondly, it's too easy to make a mistake – [the guys from PVS Studio wrote about it once](https://m.vk.com/away.php?to=https%3A%2F%2Fpvs-studio.com%2Fru%2Fblog%2Fterms%2F0068%2F)

{% highlight python %}
if v == 'a':
    self.value_a = 1
elif v == 'b':
    self.value_b = 1
elif v == 'c':
    self.value_c = 1
{% endhighlight %}

In most cases, all the repeating fragments that you copy-pasted can be easily combined into one code snippet, either through dictionaries using some kind of mapping, or through getattr / setattr, or by inserting the repeating code into a separate function and calling it with parameters. In other words, the repeating code is converted to a general form and then gets some parameters.

{% highlight python %}
setattr(self, f'value_{v}', 1)
{% endhighlight %}

## Lines of code interconnected to each other
This point is simple – sometimes lines of code are related to each other, that is, when you change one of them, the other one also needs to be changed. It is clear that at some point you will change one line and forget to change the other. That is why you need to get rid of such dependencies, and to solve this problem, people usually make a reference from one line of code to another:

{% highlight python %}
rows = [
    {'col 1': 'value 1', 'col 2': 'value 2'},
    {'col 1': 'value 3', 'col 2': 'value 4'},
]

writer = csv.DictWriter(..., fieldnames=['col 1', 'col 2'])
{% endhighlight %}

Now if you decide to add an additional column to rows or rename an existing one, you will also need to change fieldnames. Instead, we'll change the code so that fieldnames will "reference" to the values we want:

{% highlight python %}
rows = [
    {'col 1': 'value 1', 'col 2': 'value 2'},
    {'col 1': 'value 3', 'col 2': 'value 4'},
]

writer = csv.DictWriter(..., fieldnames=rows[0].keys())
{% endhighlight %}

## Hints typing
In this case, everything is extremely simple: you must always use type hints. You can have fun when writing code, but when your code is being read (even by yourself a year later), it's often very difficult to understand what arguments the function has and what type they are. In this regard, from type hints you'll get at least a little help.

{% highlight python %}
def foo(processor):
    # wtf is processor? what is returned?
    ...


def foo(processor: Processor) -> str:
  	# okay, now I can jump to definition of Processor and see what it is
    ...
{% endhighlight %}

I write "at least" because type hints like Tuple[int, str, datetime] don't tell you what exactly will be returned (object_id, name, creation_datetime). Using a namedtuple would help here, but we only need to return values ​​and this looks somewhat like overengineering.

## Quick check with "in"
Extremely simple: do you need to check for the presence of something in something? It's better to use sets, since the execution time of the "element in set" operation is constant. By comparison, in the case of the "element in list" operation, you invoke an element-by-element comparison monster, and with a large number of elements in the list, this monster will bite your leg sooner or later.

Not nice:

{% highlight python %}
items = [instance1, instance2, instance3]
if instance4 in items:
    print('yes')
{% endhighlight %}

Nice:

{% highlight python %}
items = {instance1.id, instance2.id, instance3.id}
if instance4.id in items:
    print('yes')
{% endhighlight %}

## Bulk
This is the advice everyone should follow at any times. It looks like this: always write code for millions of objects, even if you only have two of them at the moment.

Are you uploading files to cloud storage? Do it using multiple threads, as if you have millions of files to deal with.

Writing a SQL query? Compose your joins in such a way as if you had millions of entries in each table.

Views for Django? Write them in such a way as if they would be called by millions of users.

Creating some code for data downloading? Do it in such a way as if you are planning to download millions of rows.

It's not so difficult, you only need to get used to. In the case of SQL it would be JOIN and INDEX commands, for Django it would be the select_related, only, values_list, update and bulk_create, and somewhere else it would be the ThreadPoolExecutor. This is more difficult option than working *with each object separately*, sometimes you'll have to split the input data into "chunks", in other cases it is better to send it in parallel, but your code will be able to work equally well with one object or with millions of them. You may not have millions of data files, but one day there will be a thousand instead of two, and your code will still run just as fast.

## Safe concurrency
One day people will run your code not in your favorite terminal, but in many threads. Or in multiple processes. With the use of different machines. What can happen when you try to process the same data in parallel? Or send the same requests to external APIs? Or access the same files on your hard drive?

Maybe nothing. Maybe nothing good.

Imagine that the execution of your program is interrupted after each line, and control passes to another program, which may be exactly the same. Now write your code with consideration of this possibility. In order to help you, people create such things as, for example, threading.Lock, @transaction.atomic, SELECT FOR UPDATE, etc.

## Asserts everywhere
In the last paragraph, let's talk a little about assert. I think this is a great feature, because I like the "either good or nothing" approach, and assert is just about that - you either do everything well, or everything falls down, and there is no third option.
Are you expecting certain data from an external service? Add an assert:

{% highlight python %}
result = get(service_url).json()
assert result in {'ok', 'fine', 'good'}
{% endhighlight %}

Do you expect the response to have at least one Item? Add an assert:

{% highlight python %}
items = Item.objects.all()
assert len(items)
print(items[0])
{% endhighlight %}

Have you written code that you're not able to understand by yourself? Add an assert:

{% highlight python %}
result = foo(bar(baz(x)))
assert result is not None
{% endhighlight %}

The general rule is: if you're *expecting* something or you're *not sure* about something, add an assert.

It's worth mentioning that asserts can be turned off using the -O spell, but if you meet people who do that, give them my warm regards and a little poison.

## What it's all about
As you can see, the purpose of these rules is to improve readability of the code, unload the programmer's brain and reduce the number of errors. There is no rocket science and all the tips are really simple, but if you stick to them, it will be much easier for me to review your code if it appears on my desk one day :)

https://habr.com/ru/post/564598/