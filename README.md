# Godot Doctor 👨🏻‍⚕️🩺 

A powerful scene validation plugin for Godot that provides a cleaner, more maintainable alternative to `_get_configuration_warnings()`, that doesn't require gameplay scripts to be `@tool`.

## Quickstart 🚀 

1. Copy the `addons/godot_doctor` folder to your project's `addons/` directory
2. Enable the plugin in Project Settings > Plugins

## Table of Contents
- [What is Godot Doctor?](#what-is-godot-doctor)
- [Why Use Godot Doctor?](#why-use-godot-doctor)
  - [🏷️ No `@tool` Required](#-no-tool-required)
  - [🔄 Automatic Scene Validation](#-automatic-scene-validation)
  - [⚙️ Validate Nodes AND Resources](#️-validate-nodes-and-resources)
  - [🧪 Test-Driven Validation](#-test-driven-validation)
  - [🎯 Declarative Syntax](#-declarative-syntax)
- [Syntax](#syntax)
  - [ValidationCondition](#validationcondition)
  - [Simple Helper Method](#simple-helper-method)
  - [Reuse validation logic with Callables](#reuse-validation-logic-with-callables)
  - [Abstract Away Complex Logic](#abstract-away-complex-logic)
  - [Nested Validation Conditions](#nested-validation-conditions)
- [How It Works](#how-it-works)
- [Examples](#examples)
- [Installation](#installation)
- [License](#license)

## What is Godot Doctor?

Godot Doctor is a Godot plugin that validates your scenes and nodes using a declarative, test-driven approach. Instead of writing procedural warning code, you define validation conditions using callables that focus on validation logic first, with error messages as metadata.

## Why Use Godot Doctor?

### 🏷️ **No `@tool` Required**
Unlike [`_get_configuration_warnings()`](https://docs.godotengine.org/en/4.5/classes/class_node.html#class-node-private-method-get-configuration-warnings), Godot Doctor works without requiring the [`@tool`](https://docs.godotengine.org/en/4.5/tutorials/plugins/running_code_in_the_editor.html#what-is-tool) annotation on your scripts.
This means that you no longer have to worry about your gameplay code being muddied by editor-specific logic.

See the difference for yourself:

![Before and After Godot Doctor](./github-assets/png/before-after.png)

Our gameplay code stays much more clean and focused!

### 🔄 Automatic Scene Validation
Validations run automatically when you save scenes, providing immediate feedback during development.
Errors are displayed in a dedicated dock, and you can click on them to navigate directly to the problematic nodes.

![Godot Doctor Example Gif](./github-assets/gif/doctor-example.gif)

### ⚙️Validate Nodes AND Resources 

Godot Doctor can not only validate nodes in your scene, but `Resource` scripts can define their own validation conditions as well. 
Very useful for validating whether your resources have conflicting data (i.e. a value that is higher than the maximum value), or missing references (i.e. an empty string, or a missing texture).

### 🧪 Test-Driven Validation
Godot Doctor encourages you to write validation logic that resembles unit tests rather than write code that returns strings containing warnings. This encourages:
- Testable validation logic
- Organized code
- Better maintainability 
- Human-readable validation conditions
- Separation of concerns between validation logic and error messages

### 🎯 Declarative Syntax

Where `_get_configuration_warnings()` makes you write code that generates strings, Godot Doctor lets you design your validation logic separately from the error messages, making it easier to read and maintain.

## Syntax

### ValidationCondition

The core of Godot Doctor is the `ValidationCondition` class, which takes a callable and an error message:

```gdscript
# Basic validation condition
var condition = ValidationCondition.new(
    func(): return health > 0,
    "Health must be greater than 0"
)
```

### Simple Helper Method

For basic boolean validations, use the convenience `simple()` method, allowing you to skip the `func()` wrapper: 

```gdscript
# Equivalent to the above, but more concise
var condition = ValidationCondition.simple(
    health > 0,
    "Health must be greater than 0"
)
```

### Reuse validation logic with Callables 

Using `Callables` allows you to reuse common validation methods:

```gdscript
func _is_more_than_zero(value: int) -> bool:
	 return value > 0

var condition = ValidationCondition.simple(
	 _is_more_than_zero(health),
	 "Health must be greater than 0"
)
```

### Abstract Away Complex Logic

Or abstract away complex logic into separate methods:

```gdscript
var condition = ValidationCondition.new(
	 complex_validation_logic,
	 "Complex validation failed"
)

func complex_validation_logic() -> bool:
	# Complex logic here
```

### Nested Validation Conditions

Making use of variatic typing, Validation conditions can return arrays of other validation conditions, allowing you to nest validation logic where needed:

```gdscript
ValidationCondition.new(
			func() -> Variant:
				if not is_instance_valid(my_resource):
					return false
				return my_resource.get_validation_conditions(),
			"my_resource must be assigned."
		)
```

## How It Works

1. **Automatic Discovery**: When you save a scene, Godot Doctor scans all nodes for the `_get_validation_conditions()` method
2. **Instance Creation**: For non-`@tool` scripts, temporary instances are created to run validation logic
3. **Condition Evaluation**: Each validation condition's callable is executed
4. **Error Reporting**: Failed conditions display their error messages in the Godot Doctor dock
5. **Navigation**: Click on errors in the dock to navigate directly to the problematic nodes

## Examples

For detailed examples and common validation patterns, see [the examples README](./addons/godot_doctor/examples/README.md).

## Installation

1. Copy the `addons/godot_doctor` folder to your project's `addons/` directory
2. Enable the plugin in Project Settings > Plugins
3. The Godot Doctor dock will appear in the editor's left panel
4. Start adding `_get_validation_conditions()` methods to your scripts, then save your scenes to see validation results!

## License

Godot Doctor is released under the MIT License. See the LICENSE file for details.
