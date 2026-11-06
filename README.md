
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

## Global Data Problem

When accessing mutable global variables you can easily make code unpredictible as it directly accesses constantly changing data. By implelmenting an input paramteter you can more safely access global data.

## Global Data Refactor

Encapulating it into a class or using a controller pattern is a great way to remove global data as in doing so the data can mutated much more predictable and easier to debug.

## Global Data Typescript Example

    let currentTaxRate = 0.05; 

    function calculateSubtotal(price: number, quantity: number): number {
        return price * quantity;
    }

    function calculateTotalWithTax(price: number, quantity: number): number {
        const subtotal = calculateSubtotal(price, quantity);
        return subtotal + (subtotal * currentTaxRate);
    }

    function applyRegionalTax(region: string): void { 
        if (region === "NY") {
            currentTaxRate = 0.08;
        } else if (region === "CA") {
            currentTaxRate = 0.0725;
        }
    }

    console.log(calculateTotalWithTax(100, 1)); // Output: 105
    applyRegionalTax("NY");
    console.log(calculateTotalWithTax(100, 1)); // Output: 108

## Global Data Typescript refactor

    interface TaxConfig {
        readonly taxRate: number; 
    }

    function calculateTotalWithTax(price: number, quantity: number, config: TaxConfig): number {
        const subtotal = price * quantity;
        return subtotal + (subtotal * config.taxRate);
    }

    function getTaxConfigForRegion(region: string): TaxConfig {
        if (region === "NY") return { taxRate: 0.08 };
        if (region === "CA") return { taxRate: 0.0725 };
        return { taxRate: 0.05 }; 
    }

    const defaultTaxConfig = getTaxConfigForRegion("DEFAULT");
    console.log(calculateTotalWithTax(100, 1, defaultTaxConfig)); // Output: 105

    const nyConfig = getTaxConfigForRegion("NY");
    console.log(calculateTotalWithTax(100, 1, nyConfig)); // Output: 108

## Global Data Python example

    current_user = "Guest"

    def greet_user():
        print(f"Hello, {current_user}")

    def login(name: str):
        global current_user
        current_user = name

    greet_user()  # Output: Hello, Guest
    login("Alice")
    greet_user()  # Output: Hello, Alice

## Global Data Python refactor

    class UserManager:
        def __init__(self, current_user: str = "Guest"):
            self.current_user = current_user

        def greet_user(self):
            print(f"Hello, {self.current_user}")

        def login(self, name: str):
            self.current_user = name

    manager = UserManager()
    manager.greet_user()  # Output: Hello, Guest
    manager.login("Alice")
    manager.greet_user()  # Output: Hello, Alice

## Duplicated Code

If you see the same code structure in more than one place, you can be sure that
your program will be better if you find a way to unify them. Duplication means
that every time you read these copies, you need to read them carefully to see if
there’s any difference. If you need to change the duplicated code, you have to
find and catch each duplication.
The simplest duplicated code problem is when you have the same expression
in two methods of the same class. Then all you have to do is Extract Function
(106) and invoke the code from both places. If you have code that’s similar, but
not quite identical, see if you can use Slide Statements (223) to arrange the code
so the similar items are all together for easy extraction. If the duplicate fragments
are in subclasses of a common base class, you can use Pull Up Method (350) to
avoid calling one from another.

## Duplicated Code Problem

By using duplicated code it can make the code harder to read as it contains unessesary information and can potentially make it harder to change in the future as you have to modify all instances of the duplication rather than changing a single funciton.

## Duplicated Code Refactor

Find the duplication and create a single piece of logic for that is responsible for that behavior, for example a funciton.

## Duplicated Code Typescript problem

function printUserReport(user: { name: string; age: number }) {
    console.log("-------------------");
    console.log("REPORT HEADER");
    console.log("-------------------");
    console.log(`User: ${user.name}, Age: ${user.age}`);
    console.log("-------------------");
    console.log("REPORT FOOTER");
}

function printAdminReport(admin: { name: string; role: string }) {
    console.log("-------------------");
    console.log("REPORT HEADER");
    console.log("-------------------");
    console.log(`Admin: ${admin.name}, Role: ${admin.role}`);
    console.log("-------------------");
    console.log("REPORT FOOTER");
}

## Duplicated Code Typescript refactor

function printReport(content: string): void {
    console.log("-------------------");
    console.log("REPORT HEADER");
    console.log("-------------------");
    console.log(content);
    console.log("-------------------");
    console.log("REPORT FOOTER");
}

function printUserReport(user: { name: string; age: number }): void {
    printReport(`User: ${user.name}, Age: ${user.age}`);
}

function printAdminReport(admin: { name: string; role: string }): void {
    printReport(`Admin: ${admin.name}, Role: ${admin.role}`);
}

## Duplicated Code Python problem

def process_admin_login(username: str):
    if not username or len(username) < 3:
        raise ValueError("Invalid username")
    print(f"Admin {username} logged in successfully.")

def process_guest_login(username: str):
    # Duplicated validation logic (exact same block)
    if not username or len(username) < 3:
        raise ValueError("Invalid username")
    print(f"Guest {username} logged in successfully.")

## Duplicated Code Python refactor

def validate_username(username: str):
    if not username or len(username) < 3:
        raise ValueError("Invalid username")

def process_login(role: str, username: str):
    validate_username(username)
    print(f"{role.capitalize()} {username} logged in successfully.")
