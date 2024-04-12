TypeScript is an open-source programming language developed and maintained by Microsoft that extends the capabilities of JavaScript. It's a strongly typed and object-oriented language. It is a superset of JavaScript, which means any valid JavaScript code is also valid TypeScript code. We use Typescript to help catch errors early through a type checking process during development, leading to more robust, maintainable, and scalable applications. This section provides an overview of TypeScript best practices.

## Always Describe Your Data (Use Strict Typing)
Avoid using `any` as much as possible. The `any` type disables TypeScript's static typing system. While it might be tempting to use `any` to quickly bypass compilation errors, doing so negates the benefits of TypeScript.
```
// Avoid
let data: any;

// Recommended
let data: string;
```

## Leverage Interface for Object Shapes
Interfaces in TypeScript are powerful tools for defining the shapes of objects. Use interfaces to ensure objects have the expected structure. In the example below, an interface is used to standardize the users to ensure that constants provide the expected structure.
```
interface User {
  fullName: string;
  age: number;
}

const user: User = {
  fullName: "Elise Co",
  age: 30,
};
```

Some properties can only be modified when an object is first created. You can specify this by putting `readonly` before the name of the property, as the following example shows.
```
interface Employee {
  readonly idNumber: number;
  readonly age: number;
}
```

## Prefer `const` and `let` over `var`
Just like in modern JavaScript, prefer `const` for variables that never change their reference and `let` for variables whose reference will change. Avoid using `var`, which has function scope, in favor of block-scoped `const` and `let`.

## Utilize Union and Intersection Types
Union and intersection types allow you to create complex type scenarios. Union types are used when a value can be one of several types while intersection types combine multiple types into one.
```
// Union type
type StringOrNumber = string | number;


// Intersection type
interface BusinessPartner {
  name: string;
  credit: number;
}

interface Identity {
  id: number;
}

type Employee = BusinessPartner & Identity;
```

## Use utility types
Utility types in TypeScript are predefined type functions that perform transformations and operations on existing types. This helps you create new types based on existing types. For example, you can change or extract properties, make properties optional or required, or create immutable versions of types. By using utility types, you can define more precise types and catch potential errors at compile time.

### Partial Type
`Partial` makes all properties of an input type `Type` as optional. This utility returns a type that represents all subsets of a given type. Below is an example of Partial.

```
interface User {
  name: string;
  age: number;
  gender: string;
  nickName: string;
}

let user: Partial<User> = {};
```

### Required Type
`Required` does the opposite of `Partial`. It makes all members of an input type `Type` non-optional (in other words, required). The following is an example of `Required`.
```
interface User {
  name: string;
  age: number;
  gender: string;
  nickName?: string;
}

let user: Required<User> = { 
  name: "Elise Co",
  age: 5,
  gender: "Male",
  nickName "Elly", // "Required" forces weight to be defined
};
```

## Generics for Reusable Code
Generics allow you to create components that work with any data type. Use generics to write flexible, reusable functions, classes, and interfaces.
```
function insertAtBeginning<T>(array: T[], value: T) {
  return [value, ...array];
}
```

## Use TSLint/ESLint and Prettier
[TSlint](https://www.npmjs.com/package/tslint)/[ESLint](https://www.npmjs.com/package/eslint) statically analyzes your code to quickly find issues. You can use ESLint to create a series of assertions (called lint rules) that define how your code should look or behave. ESLint also has auto-fixer suggestions to help you improve your code. Finally, you can use ESLint to load in lint rules from shared plugins.

[Prettier](https://www.npmjs.com/package/prettier) is an opinionated code formatter that supports many languages and integrates with most editors. Its primary goal is to eliminate discussions about code formatting by enforcing a consistent style. By doing so, it helps improve code readability and reduces the time developers spend formatting their code manually.

## Organize Imports
Keep your imports organized and avoid circular dependencies. Consider using tools or IDE features that automatically organize and clean up unused imports. We recommend to activate `sort-imports` rule in your `eslint` to remind you of this while developing

## Use Destructuring on Properties
Destructuring, introduced in ECMAScript 6 (ES6), is a JavaScript feature that gives you the ability to extract multiple pieces of data from an array or object and assign them to their own variables.
```
const user: User = {
  fullName: "Elise Co",
  age: 25,
};

// Instead of this
const fullName = user.fullName;
const age = user.age;

// We destructure
const { fullName, age } = object;
```

## Standard Naming Conventions
Enforcing a naming convention keeps the code base consistent and reduces overhead when thinking about how to name a variable. We recommend the following:
1. Use camelCase for variable and function names.
2. Use PascalCase for class names and interface names.
3. Use camelCase for interface properties.
4. Use PascalCase for type names and enum names.

