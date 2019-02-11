# Python stuff
## Non local variables 
In python, nested functions can access variables of the enclosing scope, i.e. the outer function basically. They can be read by default but if you want to modify them then you need to use a nonlocal keyword. 

The use of nonlocal keyword is like using global keyword.  `nonlocal` is used to declare that a variable inside a nested function is not local to it, meaning it lies in the outer enclosing function. If this isn’t used then a new variable is created, inside the nested function. 

```
def outer_function():
    a = 5
    def inner_function():
		  print("Inner function, before non local: ", a)
        nonlocal a
        a = 10
        print("Inner function: ",a)
    inner_function()
    print("Outer function: ",a)

outer_function()


## This will output:
## Inner function, before non local: 5 (Note you can access a)
## Inner function: 10
## Outer function: 10
```

## Closure 
If the enclosing function returns the nested function which uses a variable in enclosing scope, the value is remembered even when the variable goes out of scope or the function itself is removed from the current namespace. 

We have a closure when:
* We have a nested function
* The nested function must refer to a value in the enclosing scope
* The enclosing function must return the nested function. 

e.g. 
```
def print_msg(msg): # This is the outer enclosing function
    def printer(): # This is the nested function
        print(msg) # Nested func. uses msg which is in enclosing scope
    return printer  # Nested func. is returned

another = print_msg("Hello")
another()

## Output:
Hello

Hello is printed, and this is the because the data gets attached to the code and is remembered when another() is executed later.
```

Closure are used in decorators. 


## *args and **kwargs
It’s a nifty way to pass in a lot of arguments. 

In python, if you have a list, say `blog = ['a', 'b']` then doing `print(*blog)` is gonna give you `a b`, i.e. using a * unpacks a list and using a ** unpacks a dictionary values. (I think)

*args:
```
blog_1 = 'I am so awesome'
blog_2 = 'Cars are cool'
blog_3 = 'Aww look at my cat'

site_title = 'My blog'

def blog_posts(title, *args):
	print(title)
	for post in args:
		print(post)

# Now you can call blog_posts with unlimited arguments  
blog_posts(site_title, blog_1, blog_2, blog_3) 

```
 

**kwargs:
```
site_title = 'My blog'

def blog_posts(title, **kwargs):
	print(title)
	for p_title, post in kwargs.items():
		print(p_title, post)

# Now you can call blog_posts with unlimited arguments  
blog_posts(
			site_title, 
			blog_1 = 'I am so awesome',
			blog_2 = 'Cars are cool',
			blog_3 = 'Aww look at my cat') 
```

Using both:
```
site_title = 'My blog'

def blog_posts(title, *args, **kwargs):
	print(title)
	for arg in args:
		print(arg)
	for p_title, post in kwargs.items():
		print(p_title, post)

# Now you can call blog_posts with unlimited arguments  
blog_posts(
			site_title, 
			'1',
			'2', 
			'3',
			blog_1 = 'I am so awesome',
			blog_2 = 'Cars are cool',
			blog_3 = 'Aww look at my cat') 
```

## Decorators
In python, functions can be passed to other functions as arguments and these are called as higher order functions.  For instance, map, reduce, filter are few examples.

So decorator function is a function, which takes in a function as an argument, adds some additional functionality to it and then executes the argument function too. 

```
def make_pretty(func):
    def inner():
        print("I got decorated")
        func()
    return inner

def ordinary():
    print("I am ordinary")

## you can ordinary function like this 
ordinary()

## If you want to decorate it, you will do this
pretty = make_pretty(ordinary)
pretty()

## Generally we decorate a function and assign it to itself. 
ordinary = make_pretty(ordinary) 

## which is basically long for 

@make_pretty
def ordinary:
	print("I am ordinary")
```
 
In case of functions with parameters you can do the following. Note this also illustrates a good use case for using decorators. 

```
def smart_divide(func):
   def inner(*args, **kwargs):
      print("I am going to divide", args[0],"and", args[1])
      if args[1] == 0:
         print("Whoops! cannot divide")
         return

      return func(*args, **kwargs)
   return inner

@smart_divide
def divide(a,b):
    return a/b
```

Another use case, which showcases chaining too:
```
def star(func):
    def inner(*args, **kwargs):
        print("*" * 30)
        func(*args, **kwargs)
        print("*" * 30)
    return inner

def percent(func):
    def inner(*args, **kwargs):
        print("%" * 30)
        func(*args, **kwargs)
        print("%" * 30)
    return inner

@star
@percent
def printer(msg):
    print(msg)
printer("Hello")


## Output:

******************************
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
Hello
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
******************************
```

## Property 
Using @property is just a pythonic way of using getters and setters.

Usually get and set operations can be programmed implicitly like this
```
class Celsius:
    def __init__(self, temperature = 0):
        self.temperature = temperature

    def to_fahrenheit(self):
        return (self.temperature * 1.8) + 32

man = Celcius()
man.temperature = 37
man.to_farenheit()
```

But then you might want to add getters and setters to do some validations while setting the property or do some complex calculations while getting the property
So now you can do the following:
```
class Celsius:
    def __init__(self, temperature = 0):
        self.set_temperature(temperature)

    def to_fahrenheit(self):
        return (self.get_temperature() * 1.8) + 32

    # new update
    def get_temperature(self):
        return self._temperature

    def set_temperature(self, value):
        if value < -273:
            raise ValueError("Temperature below -273 is not possible")
        self._temperature = value
```

Note how the private variable `_temperature` is used. (There is no such concept in python though).

However now, you can’t do `man.temperature` in the above case. Property comes to rescue for that.

```
class Celsius:
    def __init__(self, temperature = 0):
        self._temperature = temperature

    def to_fahrenheit(self):
        return (self.temperature * 1.8) + 32

    @property
    def temperature(self):
        print("Getting value")
        return self._temperature

    @temperature.setter
    def temperature(self, value):
        if value < -273:
            raise ValueError("Temperature below -273 is not possible")
        print("Setting value")
        self._temperature = value
```

```
## Now

man = Celcius()
man.temperature = 37 # this works along with input validation 
```
#python 
