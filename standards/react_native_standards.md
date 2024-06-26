# Style Guide

This style guide provides a comprehensive overview of best practices, coding standards, and recommendations for working with React Native. It aims to help developers write clean, efficient, and maintainable code.

## General Principles

- **Component-Based Development**: Break down your UI into reusable components, each responsible for a specific piece of functionality or view. This approach promotes code reusability, maintainability, and scalability.
  Symph follows Atomic Design, a file structure methodology that breaks down all the elements of a page into simple buckets. It is a way to compose/split a set of components into:
    > `Atoms` <br>
    > `Molecules` <br>
    > `Organisms` <br>
    > `Template` <br>
    > `Screen/Page`
- **State Management**: Keep state as localized as possible to avoid unnecessary re-renders and maintain a clear data flow.
  Utilize global state management solutions, such as user data stored in a centralized store using libraries like `React Context API`, for managing shared state across multiple components.
- **Performance Optimization**: Minimize unnecessary re-renders, efficiently render large lists of data, and profile and optimize performance bottlenecks using react tools.
- **Platform-Specific Considerations**: Utilize platform-specific components or styles when necessary to accommodate differences between iOS and Android.
- **Testing and Debugging**: Write comprehensive tests to ensure the reliability and stability of your React Native application. Use the following preferred testing libraries:
    > `Jest`: For unit testing individual functions, components, and services. <br>
    > `React Native Testing Library`: For component testing to ensure UI components render correctly. <br>
    > `Appium`: For end-to-end (E2E) testing to simulate real user scenarios and interactions.<br>

## Basic Rules

- Always use `const` or `let` to declare variables. Use const by default, unless a variable needs to be reassigned.
- Always use functional component.
- No nested components or getComponent inside render method. Always try to separate the components if possible. Here's a sample code to illustrate this:

  `ParentComponent.tsx`
  ```csharp
  import React from 'react';
  import Header from './Header';
  import Content from './Content';
  import Footer from './Footer';

  const ParentComponent = () => {
    return (
      <div>
        <Header />
        <Content />
        <Footer />
      </div>
    );
  };

  export default ParentComponent;
  ```

  `Header.tsx`
  ```csharp
  import React from 'react';

  const Header = () => {
    return (
      <header>
        <h1>My App</h1>
      </header>
    );
  };

  export default Header;
  ```

  `Content.tsx`
  ```csharp
  import React from 'react';
  import SubContent from './SubContent';

  const Content = () => {
    return (
      <main>
        <SubContent />
      </main>
    );
  };

  export default Content;
  ```

  `SubContent.tsx`
  ```csharp
  import React from 'react';

  const SubContent = () => {
    return (
      <section>
        <p>This is some sub content.</p>
      </section>
    );
  };

  export default SubContent;
  ```

  `Footer.tsx`
  ```csharp
  import React from 'react';

  const Footer = () => {
    return (
      <footer>
        <p>© 2024 My App</p>
      </footer>
    );
  };

  export default Footer;
  ```
## Naming Conventions

- Component’s names should be written using `pascal case`.
  >`Header.tsx` <br>
  >`CustomButton.tsx` <br>
  >`Footer.tsx`

- Non-component’s name should be written using `camel case`.
  > `myUtilityFile.ts` <br>
  > `cookieHelper.ts` <br>
  > `fetchApi.ts`

- Unit test files should use the same name as its corresponding file. Preferably, it should be placed in a separate `__tests__` folder.
  Example:
  > `components/HeroBanner.tsx` <br>
  > `components/__tests__/HeroBanner.test.ts` <br>

  > `components/CookieBanner.tsx` <br>
  > `components/__tests__/CookieBanner.test.ts`

- Global StyleSheet file should be written using `camel case`.
  > `globalStyle.ts` <br>

- All fonts, colors, and font sizes should be in one file.
  > `fontsConfig.ts`

- Attribute name should be `camel case`.
  > `className` <br>
  > `onClick`

- Variable names should be `camel case`. Variable names can contain number and special characters.
  > `const variable = 'test';` <br>
  > `let variableBoolean = true;`

- Use `.tsx` extension a for React components

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
  import { testFunction } from './tests';
  ```

  To ensure that imports from test files are only used in test files, use the `eslint-plugin-import` and configure it in the ESLint setup. Add the following configuration to your ESLint configuration file (e.g., `.eslintrc.js`):

  ```csharp
  module.exports = {
    // other configurations...
    plugins: ['import'],
    rules: {
      'import/no-extraneous-dependencies': [
        'error',
        {
          devDependencies: [
            '**/__tests__/**', // For Jest tests
            '**/?(*.)+(spec|test).[jt]s?(x)', // For spec/test files
          ],
          optionalDependencies: false,
          peerDependencies: false,
        },
      ],
    },
  };
  ```

  This configuration ensures that `import { testFunction } from './tests';` is only allowed in test files, helping to maintain a clear separation between production and test code.

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

  The following ESLint and Prettier configurations ensure this convention is automatically enforced:

  ### ESLint Configuration

  Add the following rules to your ESLint configuration file (e.g., `.eslintrc.js`):

  ```csharp
  module.exports = {
    // other configurations...
    rules: {
      // Enforce single quotes for JavaScript code
      'quotes': ['error', 'single', { 'avoidEscape': true, 'allowTemplateLiterals': true }],
      // Enforce double quotes for JSX attributes
      'jsx-quotes': ['error', 'prefer-double'],
    },
  };
  ```

  ### Prettier Configuration

  Add the following configuration to your Prettier configuration file (e.g., `.prettierrc`):

  ```csharp
  {
    "singleQuote": true,
    "jsxSingleQuote": false
  }
  ```

## Crash Reporting Tools

- Crash analytics tools are like superhero sidekicks for your app, always watching for any trouble 24/7. They quickly catch and understand app crashes and errors in real-time, helping you figure out what went wrong.
- Some cool crash analytics tools are `Sentry` and `Firebase Crashlytics`.