
# BAD CODE SMELLS

## Global Data

Since our earliest days of writing software, we were warned of the perils of
global data—how it was invented by demons from the fourth plane of hell, which
is the resting place of any programmer who dares to use it. And, although we
are somewhat skeptical about fire and brimstone, it’s still one of the most pungent
odors we are likely to run into. The problem with global data is that it can be
modified from anywhere in the code base, and there’s no mechanism to discover
which bit of code touched it. Time and again, this leads to bugs that breed from
a form of spooky action from a distance—and it’s very hard to find out where the
errant bit of program is. The most obvious form of global data is global variables,
but we also see this problem with class variables and singletons.
Chapter 3 Bad Smells in Code74
Our key defense here is Encapsulate Variable (132), which is always our first
move when confronted with data that is open to contamination by any part of a
program. At least when you have it wrapped by a function, you can start seeing
where it’s modified and start to control its access. Then, it’s good to limit its
scope as much as possible by moving it within a class or module where only that
module’s code can see it.
Global data is especially nasty when it’s mutable. Global data that you can
guarantee never changes after the program starts is relatively safe—if you have
a language that can enforce that guarantee.
Global data illustrates Paracelsus’s maxim: The difference between a poison
and something benign is the dose. You can get away with small doses of global
data, but it gets exponentially harder to deal with the more you have. Even with
little bits, we like to keep it encapsulated—that’s the key to coping with changes
as the software evolves.

## Problem

When accessing mutable global variables you can easily make code unpredictible as it directly accesses constantly changing data. By implelmenting an input paramteter you can more safely access global data.

## Bad Example

    let currentUser = "Guest"

    function greetUser(){
        console.log("Hello," + currentUser)
    }

    function login(name: string){
        currentUser = name;
    }
    
## Refactored Code

    let currentUser = "Guest"

    function greetUser(user: string){
        console.log("Hello," + user)
    }

    function login(name: string){
        currentUser = name;
    }

    ## Duplicated Code

Duplicated Code occurs when the same or very similar logic appears in multiple places. It can cause bugs when someone updates one copy but misses another.

Dup code occurs when the same or similar logic appears in multiple places. It can cause bugs when someone updates one copy but misses other

# Prevention and Refactoring


Look for previous and repeat code when reviewing. Use Fowler's Extract Function refactoring to move shared logic, call it wherever needed. Avoid combining code that look similar but has different behavior

# Python example

**Before:**

```python
def student_price(price):
    return price - price * 0.10

def member_price(price):
    return price - price * 0.10

Before like both use the same number so when u fix one u have to fix other


**After:**

def apply_discount(price, discount_rate):
    return price - price * discount_rate

def student_price(price):
    return apply_discount(price, 0.10)

def member_price(price):
    return apply_discount(price, 0.10)

So now a change to the formula only needs to happen once.
