# RBX.Lua Style Guide

This is a style guide for ROBLOX's variant of Lua. These guidelines are to keep code consistent across projects, and allow contributors to learn exactly how to write code for those projects.

This style guide is based off of [Olivine Lab's Lua Style Guide](https://github.com/Olivine-Labs/lua-style-guide).

## Table of Contents

  1. [Strings](#strings)
  1. [Functions](#functions)
  1. [Properties](#properties)
  1. [Variables](#variables)
  1. [Classes](#classes)
  1. [Naming Conventions](#naming-conventions)
  1. [Conditional Expressions & Equality](#conditional-expressions--equality)
  1. [Blocks](#blocks)
  1. [Whitespace](#whitespace)

## Strings

- Use double quotes for strings.

  ```lua
  -- bad
  local name = 'John'

  -- good
  local name = "John"
  ```

- Strings longer than 80 characters should be concatenated. Do not use double-brackets (`[[]]`) or backslashes (`\`), line breaks and whitespace will effect them.

  ```lua
  -- bad
  local str = "This is an incredibly long string that exceeds 80 characters and just keeps going. This string has no regard for your well being and will stop at nothing to make your life a living hell."

  -- bad
  local str = [[This is an incredibly long string that exceeds 80 characters
  and just keeps going. This string has no regard for your well being and will
  stop at nothing to make your life a living hell.]]

  -- bad
  local str = "This is an incredibly long string that exceeds 80 characters \
  and just keeps going. This string has no regard for your well being and will \
  stop at nothing to make your life a living hell."

  -- good
  local str = "This is an incredibly long string that exceeds 80 characters "..
    "and just keeps going. This string has no regard for your well being and "..
    "will stop at nothing to make your life a living hell."
  ```

**[[⬆]](#table-of-contents)**

## Functions

- Prefer lots of small functions to large, complex functions. Consider reading [Small Functions are Good for the Universe](http://kiki.to/blog/2012/03/16/small-functions-are-good-for-the-universe/).

- Prefer function syntax over variable syntax.

  ```lua
  -- bad
  local nope = function()
    -- code
  end

  -- good
  local function yup()
    -- code
  end
  ```

- Perform validation early and return as soon as possible.

  ```lua
  -- bad
  local function isGoodName(name)
    local isGood = #name > 3
    isGood = isGood and #name < 30

    -- code

    return isBad
  end

  -- good
  local function isGoodName(name)
    if #name < 3 or #name > 30 then return false end

    -- code

    return true
  end
  ```

- Prefer defining functions after variables.

  ```lua
  -- bad
  local function foo()
    -- code
  end

  local bar = true
  local baz = "something"

  -- good
  local bar = true
  local baz = "something"

  local function foo()
    -- code
  end
  ```

**[[⬆]](#table-of-contents)**

## Properties

- Use dot notation when accessing known properties.

  ```lua
  local luke = {
    IsJedi = true,
    Age = 28
  }

  -- bad
  local isJedi = luke["IsJedi"]

  -- good
  local isJedi = like.IsJedi
  ```

- Use subscript notation (`[]`) when accessing properties with a variable.

  ```lua
  local luke = {
    IsJedi = true,
    Age = 28
  }

  local function getProp(prop)
    return luke[prop]
  end

  local isJedi = getProp("IsJedi")
  ```

**[[⬆]](#table-of-contents)**

## Variables

- Always use `local` to declare variables.

  ```lua
  -- bad
  superPower = SuperPower.new()

  -- good
  local superPower = SuperPower.new()
  ```

- Group top-level variables by services, dependencies, constants, and globals. Separate each group by a line space.

  ```lua
  -- bad
  local CONSTANT = true
  local global = "This can be changed"
  local run = game:GetService("RunService")
  local replicatedStorage = game:GetService("ReplicatedStorage")
  local module = require(replicatedStorage.ModuleScript)

  -- good
  local run = game:GetService("RunService")
  local replicatedStorage = game:GetService("ReplicatedStorage")

  local module = require(replicatedStorage.ModuleScript)

  local CONSTANT = true

  local global = "This can be changed"
  ```

- Assign variables at the top of their scope when possible. Scattering them around makes functions hard to parse.

  ```lua
  -- bad
  local function bad()
    test()
    print("doing stuff")

    -- code

    local name = getName()

    if name == "test" then
      return false
    end

    return name
  end

  -- good
  local function good()
    local name = getName()

    test()
    print("doing stuff")

    -- code

    if name == "test" then
      return false
    end

    return name
  end
  ```

**[[⬆]](#table-of-contents)**

## Classes

- All classes must define a `.new()` function for instantiation. This follows ROBLOX's convention for class constructors.

  ```lua
  -- bad
  local gal = Person("Savanna")

  -- good
  local gal = Person.new("Savanna")
  ```

## Naming Conventions

- Avoid single letter names. Be descriptive with your naming. You can get away with single-letter names when they are variables in loops.

  ```lua
  -- bad
  local function q()
    -- code
  end

  -- good
  local function query()
    -- code
  end

  -- good
  for i=1, #list do
    -- code
  end
  ```

- Use underscores for ignored variables.

  ```lua
  -- good
  local _, message = pcall(function() return error("Oops!") end)

  --good
  for _, name in pairs(names) do
    -- code
  end
  ```

- Use lowerCamelCase when naming objects, functions and instances.

  ```lua
  -- bad
  local not_like_this = {}
  local OrLikeThis = true

  --- good
  local variableWithManyWords = {}

  local function doThatThing()
    -- code
  end
  ```

- Use PascalCase for classes and properties.

  ```lua
  -- bad
  local person = require(replicatedStorage.Person)
  local guy = person.new("John")
  local name = guy.name

  -- good
  local Person = require(replicatedStorage.Person)
  local guy = Person.new("John")
  local name = gui.Name
  ```

- Use CAPITALS_AND_UNDERSCORES for constants.

  ```lua
  -- bad
  local thisWillNeverChange = false

  -- good
  local THIS_WILL_NEVER_CHANGE = true
  ```

- When defining services, remove "Service" from the name when possible.

  ```lua
  -- bad
  local contextActionService = game:GetService("ContextActionService")
  local userInputService = game:GetService("UserInputService")
  local httpService = game:GetService("HttpService")

  -- good
  local contextAction = game:GetService("ContextActionService")
  local userInput = game:GetService("UserInputService")
  local http = game:GetService("HttpService")
  ```

- Use `is` or `has` for boolean-returning functions that are part of a class.

  ```lua
  -- bad
  local function evil(alignment)
    return alignment < 100
  end

  -- good
  local function isEvil(alignment)
    return alignment < 100
  end
  ```

**[[⬆]](#table-of-contents)**

## Conditional Expressions & Equality

- `false` and `nil` are *falsy* in conditional expressions. All else is true.

  ```lua
  local str = ""

  if str then
    -- true
  end
  ```

- Use shortcuts when you can, unless you need to know the difference between `false` and `nil`.

  ```lua
  -- bad
  if name ~= nil then
    -- code
  end

  -- good
  if name then
    -- code
  end
  ```

- Prefer `true` statements over `false` statements where it makes sense. Prioritize truthy conditions when writing multiple conditions.

  ```lua
  -- bad
  if not thing then
    -- code
  else
    -- code
  end

  -- good
  if thing then
    -- code
  else
    -- code
  end
  ```

- Short ternaries are okay.

  ```lua
  local function defaultName(name)
    -- return the default "Waldo" if name is nil
    return name or "Waldo"
  end

  local function brewCoffee(machine)
    return machine and machine.isLoaded and "coffee brewing" or "fill your water"
  end
  ```

**[[⬆]](#table-of-contents)**

## Blocks

- Single line blocks are okay for *small* statements. Try to keep lines to 80 characters. Indent lines if they overflow past the limit.

  ```lua
  -- good
  if test then return false end

  -- good
  if test then
    return false
  end

  -- bad
  if test < 1 and doComplicatedFunction(test) == false or seven == 8 and nine == 10 then doOtherComplicatedFunction() end

  -- good
  if test < 1 and doComplicatedFunction(test) == false or
      seven == 8 and nine == 10 then

    doOtherComplicatedFunction()
    return false
  end
  ```

**[[⬆]](#table-of-contents)**

## Whitespace

Install [EditorConfig](http://editorconfig.org/) for your text editor to have your tabs and other settings configured automatically on a per-project basis.

- Use soft tabs set to 2 spaces.

  ```lua
  -- bad
  function()
  ∙∙∙∙local name
  end

  -- bad
  function()
  ∙local name
  end

  -- good
  function()
  ∙∙local name
  end
  ```

- Place 1 space before opening and closing braces. Place no spaces around parenthesis.

  ```lua
  -- bad
  local test = {one=1}

  -- good
  local test = { one = 1 }

  -- bad
  dog.set('attr',{
    Age = "1 year",
    Breed = "Bernese Mountain Dog"
  })

  -- good
  dog.set('attr', {
    Age = "1 year",
    Breed = "Bernese Mountain Dog"
  })
  ```

- Place an empty newline at the end of the file.

  ```lua
  -- bad
  (function(global)
    -- ...stuff...
  end)(self)
  ```

  ```lua
  -- good
  (function(global)
    -- ...stuff...
  end)(self)

  ```

- Surround operators with spaces.

  ```lua
  -- bad
  local thing=1
  thing = thing-1
  thing = thing*1
  thing = 'string'..'s'

  -- good
  local thing = 1
  thing = thing - 1
  thing = thing * 1
  thing = 'string' .. 's'
  ```

- Use one space after commas.

  ```lua
  --bad
  local thing = {1,2,3}
  thing = {1 , 2 , 3}
  thing = {1 ,2 ,3}

  --good
  local thing = {1, 2, 3}
  ```

**[[⬆]](#table-of-contents)**
