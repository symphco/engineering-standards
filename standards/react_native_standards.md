# Style Guide

This style guide provides a comprehensive overview of best practices, coding standards, and recommendations for working with React Native. It aims to help developers write clean, efficient, and maintainable code.

## General Principles

- **Component-Based Development**: Break down your UI into reusable components, each responsible for a specific piece of functionality or view. This approach promotes code reusability, maintainability, and scalability.
- **State Management**: Keep state as localized as possible to avoid unnecessary re-renders and maintain a clear data flow.
- **Performance Optimization**: Minimize unnecessary re-renders, efficiently render large lists of data, and profile and optimize performance bottlenecks using react tools.
- **Platform-Specific Considerations**: Utilize platform-specific components or styles when necessary to accommodate differences between iOS and Android.
- **Testing and Debugging**: Write comprehensive tests to ensure the reliability and stability of your React Native application.

## Basic Rules

- Always use `const` or `let` to declare variables. Use const by default, unless a variable needs to be reassigned.
- Always use functional component.
- No nested components or getComponent inside render method. Always try to separate the components if possible.

## Naming Conventions

- Component’s names should be written using `pascal case`. (e.g. `Header.js`, `CustomButton.js`, `Footer.js`)
- Non-component’s name should be written using `camel case`. (e.g. `myUtilityFile.js`, `cookieHelper.js`, `fetchApi.js`)
- Unit test files should use the same name as its corresponding file (e.g. `HeroBanner.js`-`HeroBanner.test.js`, `CookieBanner.js`-`CookieBanner.test.js`)
- Global StyleSheet file should be written using `camel case`. (e.g. `globalStyle.js`)
- All fonts, colors, and font sizes should be in one file. (e.g. `fontsConfig.js`)
- Attribute name should be `camel case`. (e.g. `className`, `onClick`)
- Variable names should be `camel case`. Variable names can contain number and special characters. (e.g. `const variable = 'test';`, `let variableBoolean = true;`) 
- Use.jsxor.tsxextension a for React components

## Files and Folders Structure

###  Grouping by Features or Navigation
- One of the common approach to structure projects is to locate all main screens in one folder, all reusable components in one folder and style related files in one folder grouped by feature or navigation.

###  Grouping by File Type
- Another popular approach to structure projects is to group similar files together.

## Importing Modules

###  Import Grouping
- Standard Libraries (React, React-Native)

```csharp
import React from 'react';
import { TouchableOpacity, View } from 'react-native';
```

- Third-Party Libraries

```csharp
import axios from 'axios';
import lodash from 'lodash';
```

- Your Own Code Imports

```csharp
import { Button, Card } from '../components';
import { MainLayout } from '../layouts';
```

- Module-Specific Imports (styles, PNG, etc.)

```csharp
import styles from'./myComponent.styles';
import image from './image.png';
```

- Test-Only Imports

```csharp
import { testFunction} from './tests';
```

### Sorting

- **Alphabetical Order**: Enforcing alphabetical order can make it easier to locate imports quickly. This could be applied within each import group.

- **Destructured Imports**: If the group contains only a few items, breaking them to one item per line may enhance readability.

- **ESLint or Prettier Rules**: Use linting tools like ESLint or code formatters like Prettier to automate and enforce coding standards. These tools often have rules or configurations for sorting and formatting imports.

## Writing with TypeScript

- To improve code readability, TypeScript comes into play. TypeScript adds types to JavaScript, enabling us to specify the type of each property in the object, reducing the possibility of errors. With TypeScript, you and your editor are aware of the object properties and their types in the code, making it easier to understand and extend the functionality.

## Path Aliases

- Using path aliases is giving your code a shortcut to make import paths shorter and more understandable. This is especially handy when you have many folders nested inside each other, making your imports easier to read.

## Usage of Double Quotes (" ") & Single Quotes (' ')
- To avoide the confusion about double quotes (" ") & single quotes (' '), use double quotes (" ") for JSX attributes and single quotes (' ') for the JS code

## Crash Reporting Tools

- Crash analytics tools are like superhero sidekicks for your app, always watching for any trouble 24/7. They quickly catch and understand app crashes and errors in real-time, helping you figure out what went wrong.
- Some cool crash analytics tools are `Sentry`, `Firebase`, `Crashlytics`, and more