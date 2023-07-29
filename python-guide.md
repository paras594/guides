
### Compiling python files

````bash
python3 file.py
````



### Comments

```python
# Single line comment
'''
this is a multiline comment
also called docstring
'''
```



### Variables

```python
# declaring variables
x = 1 # int
y = 2.5 # float
name = "Someone" # str
is_cool = True # bool

# multiple assignment
x, y, name, is_cool = (1, 2.5, "someone", True)
```



### Print to console

```python
print("hello world")
```



### Types

```python
print(type(2)) # <class 'int'>
print(type(2.5)) # ''' 'float'
print(type(True)) # 'bool'
print(type("work")) # 'str'

# type cast
num = 1
str(num) # cast to str
int(2.5) # cast to int
float(2) # to float
```



### Strings

```python
# declare string
name = "john"
age = 20

# concatenate
"something" + name

# Arguments by position
"my name is {name} and age is {age}".format(name=name, age=age)

# F strings, similar to template strings in js
f"my name is {name} and age is {age}"

# methods
s = "hello world"

s.capitalize()
s.upper()
s.lower()
s.swapcase()
len(s) # gets the length
s.replace("world", "universe")
s.count("h") # counts number of h in string
s.startswith("hello")
s.endswith("world")
s.split()
s.find("r")

```



### List (Arrays)

```python
# ordered, changeable, allows duplicate

# create
nums = [1, 2, 3]
nums[0] # 1
len(nums) # 3
nums.append(4) # similar to arr.push 
nums.remove(2)
nums.insert(2, 2.5) # index, item
nums.pop(2) # remove from 2 index by default last
nums.reverse()
nums.sort() # ascending
nums.sort(reverse = True) # descending
nums[2] = 5
```



### Tuples

```python
# ordered, unchangeable, allows duplicate

# create
fruits = ("Apples", "Oranges", "Grapes")
fruits = ("Apples",) # leave a trailing comma in case of single value
fruits[0] # Apples
```



### Set

```python
# unordered, unindexed, no duplicates
nums = {1, 2, 3, 4}

# check if element in set
1 in nums # True

# methods
nums.add(5)
nums.remove(5)
nums.clear() # empties the set
del nums # deletes the variable
```



### Dictionary (Objects in javascript)

```python
# unordered, changeable, indexed, no duplicates
# create dict
person = {
   "name": "john", # quotes are required in key
   "age": 20
}

person["name"] # john
person["hobby"] = "football" # add key value

# methods
person.keys() # get keys in dict
person.items() # [("name", "john"), ("age", 20)]
person2 = person.copy() # similar to spread operator
del(person["name"]) # delete key from dict
person.pop("age") # deletes key from dict
person.clear()
len(person)

```



### Functions 

```python
# create function
def sayHello(name="Sam"): # default arg
   print(f"Hello {name}")

sayHello("John")

# return value
def getSum(x, y): 
   total = x + y
   return total

getSum(2, 5)

# Lambdas AKA arrow functions
getSum = lambda num1, num2 : num1 + num2
```



### Conditionals

```python
if 10 > 5:
   print(10)
elif 10 === 5:
   print("equal")
else: 
   print(5)
   
# nested
if x > 2:
   if x <= 10:
      print("cool")
      
# and or not
if x > 2 and x <= 10:
   print("cool")

if not(x == 10):
   print("not equal")

nums = [1, 2, 3]
if 2 in nums : 
   print("yes")
```



### Loops

```python
# for loop
people = ["x", "y", "z"]

for person in people:
   print(person)

for person in people:
   if person == "y":
      continue
   if person == "z":
      break
   print(person)
   
for i in range(0, 10):
   print(i) # 0...9
   
for i in range(0, 10, 2): # (start, end, increment)
   print(i) # 0, 2, 4, .. 8
```



```python
# while loop
# in python there is no increment and decrement operator
while count <= 10:
   print(count)
   count += 1
```



### Modules in python

A module is basically a file containing a set of functions  to include  in your application. There are core python modules and modules that can be installed using pip package manager 

```python
# import module
import datetime

# import a part
from datetime import date
```



### OOP in Python



#### Classes

```python
# create class

class User:
   # constructor
   def __init__(self, name):
      self.name = name
   
   
# create objects
paras = User("paras")
      
```



### Setup Python Project

Using virtualenv to create environment for projects and install packages to it. Similar to using npm and keeping packages in node_modules

```bash
# install virtualenv globally
pip3 install virtualenv
```



```bash
# creating virtualenv
virtualenv venv

# activating virtualenv
source venv/bin/activate

# deactivate
deactivate
```



