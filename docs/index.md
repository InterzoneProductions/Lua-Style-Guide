## Guiding Principles
* Optimization is key, maintain good coding practices within your codebase.
* Maintain a consistent code style and maintain a clean formatting style. 
* Avoid magic values and refrain from micro-optimizing your code. Code should only be optimized if the performance gain is significant.
* Readability is king. Code within Knit should not be impressive; it should be readable. Having readable code is important for debugging and future maintainability.
* Avoid using wait() in your codebase. Use the new task library, task. wait().
* All services should be referenced using game:GetService at the top of the file.
* When importing a module, use the name of the module for its variable name.


## File Structure
1.  A file header, lets other developers know who created the code and when the script was created.
    ```lua
    -- AUTHOR
    -- SCRIPT NAME/PARENT
    -- DAY MONTH YEAR
    ```
2.  Documentation
    ```lua
    --[[
        MyModule.foo(value: number)
        MyModule.doo()
        MyModule.poo(value: number)
    --]]
    ```

3.  Services used by the file, using GetService
4.  Module imports
5.  Constants 
6.  Variables (refrain from  using global variables)
7.  Module definition (e.g. MyModule = {})
8.  Module code
9.  A return statement!


## Requires
* All requires should be at the top of the file
* Use relative paths when importing modules from the same package.
  ```lua
  local Knit = require(script.Parent.Knit)
  ```
* Use absolute paths when importing modules from a different package.
  ```lua
  local CorePackages = game:GetService("CorePackages")
  local Roact = require(CorePackages.Roact)
  ```

## Metatables
* Metatables are an incredibly powerful Lua feature that can be used to overload operators, implement prototypical inheritance, and tinker with limited object lifecycle.

## Prototype-based classes
* The most popular pattern for classes in Lua is sometimes referred to as the One True Pattern. It defines class members, instance members, and metamethods in the same table and highlights Lua's strengths well.

* First up, we create a regular, empty table:

```lua
local MyClass = {}
```

Next, we assign the `__index` member on the class back to itself. This is a handy trick that lets us use the class's table as the metatable for instances as well.

When we construct an instance, we'll tell Lua to use our `__index` value to find values that are missing in our instances. It's sort of like `prototype` in JavaScript if you're familiar.

```lua
MyClass.__index = MyClass
```

In most cases, we create a default constructor for our class. By convention, we usually call it `new`.

Methods that don't operate on instances of our class are usually defined using a dot (`.`) instead of a colon (`:`).

```lua
function MyClass.new()
    local self = {
        -- Define members of the instance here, even if they're `nil` by default.
        phrase = "bark",
    }

    -- Tell Lua to fall back to looking in MyClass.__index for missing fields.
    setmetatable(self, MyClass)

    return self
end
```

We can also define methods that operate on instances. These are just methods that expect their first argument to be an instance. By convention, we define them using a colon (`:`):

```lua
-- This is functionally identical to `function MyClass.bark(self)`
function MyClass:bark()
    print("My phrase is", self.phrase)
end
```

At this point, our class is ready to use!

We can construct instances and start tinkering with them:

```lua
local instance = MyClass.new()

-- Properties on the instance are visible since it's just a table:
print(instance.phrase) -- "bark"

-- Methods are pulled from MyClass because of our metatable:
instance:bark() -- "My phrase is bark"

-- We can also invoke methods with a dot by explicitly passing `instance`:
MyClass.bark(instance)
instance.bark(instance)
```

### Guarding Against Typos
* Indexing into a table in Lua gives you `nil` if the key isn't present, which can cause errors that are difficult to trace!

* Our other major use case for metatables is to prevent certain forms of this problem. For types that act like enums, we can carefully apply an `__index` metamethod that throws:

```lua
local MyEnum = {
    A = "A",
    B = "B",
    C = "C",
}

setmetatable(MyEnum, {
    __index = function(self, key)
        error(("%s is not a valid member"):format(key))
    end,
})
```
Since `__index` is only called when a key is missing in the table, `MyEnum.A`and `MyEnum.B` will still give you back the expected values, but `MyEnum.FROB` will throw, hopefully helping engineers track down bugs more easily.

## General Punctuation
* Don't use semicolons `;`. 

## General Whitespace
* Always indent with tabs
* Keep lines under 120 columns wide, assuming four column wide tabs.
* Wrap comments to 80 columns wide, assuming four column wide tabs.
    * This is different than normal code; the hope is that short lines help improve the readability of comment prose, but is too restrictive for code.
* Don't leave whitespace at the end of lines.
* No vertical alignment.

<span style="color:green">**Good**:</span>
```lua
local foo = 1
local woo = 2
```
<span style="color:red">**Bad**:</span>
```lua
local foo = 1
local woo       =  2
```

* Use a single empty line to express groups when useful. Do not start blocks with a blank line. Excess empty lines harm whole-file readability.

```lua
local PointsService = require(shared.PointsService)

local function foo()

end

PointsService.CheckData(player)
PointsService.CheckData(player)

PointsService.GivePoints(player,10)
```

* Use one statement per line. Put function bodies on new lines.

```lua
table.sort(stuff, function(a, b)
    local sum = a + b
    return math.abs(sum) > 2
end)
```

* This is also true for if blocks, even if their body is just a return statement. Consistency is important.

```lua
if valueIsInvalid then
    return
end
```

* Put a space before and after operators, except when clarifying precedence.

```lua
print(2 * 9 * 8 / 2)
```

* Put a space after commas in tables and function calls

```lua
local friends = {"bob", "amy", "joe"}
foo(5, 6, 7)
```

* When creating blocks, inline any opening syntax elements.

```lua
local foo = {
    bar = 2,
}

if foo then
    -- do something
end
```

* Avoid putting curly braces for tables on their own line. Doing so harms readability since it forces the reader to move to another line in an awkward spot in the statement.

<span style="color:green">**Good**:</span>
```lua
local foo = {
    bar = {
        baz = "baz",
    },
}
frob({
    x = 1,
})
```
<span style="color:red">**Bad**:</span>
```lua
local foo =
{
    bar =

    {
        baz = "baz",
    },
}

frob(
{
    x = 1,
})
```



## Blocks
* Don't use parentheses around the conditions in if, while, or repeat blocks.
* Use `do` blocks if limiting the scope of a variable is useful.

## String literals

* Use double quotes when declaring string literals.

```lua
local message = "Hi"
```

* Use single quotes when declaring string literals that represent characters.

```lua
local characterA = "A"
```

## Tables
* Avoid tables with both list-like and dictionary-like keys.
* Iterate over list-like tables with `ipairs` and dictionary-like tables with `pairs`.
* Add trailing commas in multi-line tables.

```lua
local frobs = {
    andrew = true,
    billy = true,
    caroline = true,
}
```

## Functions

* Keep the number of arguments to a given function small, preferably 1 or 2.
* Always use parentheses when calling a function. Lua allows you to skip them in many cases, but the results are typically much harder to parse.
* Declare named functions using function-prefix syntax. Non-member functions should always be local.
* When declaring a function inside a table, use function-prefix syntax. Differentiate between `.` and `:` to denote intended calling convention.

```lua 
-- This function should be called as Frobulator.new()
function Frobulator.new()
    return {}
end

-- This function should be called as Frobulator:frob()
function Frobulator:frob()
    print("Frobbing", self)
end
```

## Documentation
At the top of each source file, simple documentation should be given to show how to use the module. This should show method signatures, fields, and events. If needed, short examples of usage can be shown too.

```lua
--[[

    -- CONSTRUCTOR DEFINITIONS --

    -- FIELD DEFINITIONS --

    -- METHOD DEFINITIONS --

    -- EVENT DEFINITIONS --

    -- EXAMPLES IF NEEDED --

--]]
```

## Naming 

* Spell words fully, refrain from using abbreviations.
* Use PascalCase for class and enum-like objects.
* Use PascalCase for all Roblox Services. 
* Use camelCase for local variables, and functions.
* Use LOUD_SNAKE_CASE for local constants.
* Prefix private variables/constants with an underscore, _camelCase 

## Yielding 

* As much as possible, refrain from yielding the main thread. Use an event-based approach.
* Refrain from using wait() as it comes with performance and yielding issues, use task.wait

## Example

```lua
-- foundanerror
-- SpeedService/ReplicatedStorage
-- 8/5/2021

local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Players = game:GetService("Players")

local DataService = require(ReplicatedStorage.DataService)
local Signal = require(ReplicatedStorage.Signal)

local BASE_SPEED = 1
local UPDATE_INTERVAL = 1

local SpeedService = {}

function SpeedService.CheckSpeed(player)
    local Speed = DataService.ReturnSpeed(player)
    return Speed
end

return SpeedService
```
