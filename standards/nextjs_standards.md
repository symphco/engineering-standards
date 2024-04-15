# Next.js Standards

# Style Guide

This style guide provides a comprehensive overview of best practices, coding standards, and recommendations for working with Next.js. It aims to help developers write clean, efficient, and maintainable code.

## General Principles

- **Consistency**: Ensure consistency across your codebase in formatting, naming conventions, and file structure.
- **Readability**: Write code that is easy to read and understand, with meaningful variable and function names.
- **Simplicity**: Keep solutions simple and avoid unnecessary complexity.
- **Performance**: Optimize for speed and efficiency, considering the performance implications of your code.

## Writing

- Write clear, concise sentences and avoid tangents.
- Use simple words; for example, use "use" instead of "utilize".
- Use an active voice for readability.
- Avoid subjective words like "easy", "quick", "simple", "just", etc.
- Write in the second person (you/your) and use gender-neutral language.
- Ensure code examples are properly formatted and working2.

### Here's an example that adheres to all the writing guidelines:

- **Function** `fetchData`

- **Description**: You can use the `fetchData` function to retrieve data from a specified URL. This function uses the built-in `fetch` API and returns a promise that resolves with the fetched data.

  ```javascript
  async function fetchData(url) {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error(`An error has occurred: ${response.status}`);
    }
    return response.json();
  }
  ```

In this example, the function description is clear, concise, and uses simple words. It's written in the second person and uses an active voice. The code example is properly formatted and working.

## Code Formatting

- Use Prettier for automatic code formatting to maintain consistency.
- Configure ESLint with Next.js specific rules to catch common issues and enforce style guidelines.
- Adopt the [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript) as our preferred coding style guide.
- Ensure a consistent indentation method (e.g., 2 spaces) across the project.

## Naming Conventions

- **Files**: Use PascalCase for files (e.g., `MyComponent.ts`).
- **Directories**: Use kebab-case for directories (e.g., `my-directory`).
- **Components**: Use PascalCase for React components (e.g., `UserProfile.js`).
- **Variables and Functions**: Use camelCase for variables and functions (e.g., `getUserData`).

## File Structure & Naming Convention

- Organize project files under the `src` directory for clarity.
- Use the `pages` directory for page components, leveraging Next.js's file-based routing.
- Store static assets in the `public` directory.
- Group components into a `components` directory, categorizing further if necessary.

## Component Structure

Adopt atomic design principles when organizing components. This involves breaking down your UI into the following fundamental building blocks:

- **Atoms**: These are the basic building blocks of your application. They can be buttons, inputs, or any other HTML elements. For example, a `Button` component.

- **Molecules**: These are groups of atoms that function together as a unit. For example, a `FormInput` component that includes a label (Atom) and an input field (Atom).

- **Organisms**: These are relatively complex components that form distinct sections of an interface. They are groups of molecules and/or atoms and/or other organisms. For example, a `Header` component that includes a logo (Atom), a navigation menu (Molecule), and a search bar (Molecule).

- **Templates**: These are page-level objects that place components into a layout and articulate the design’s underlying content structure. For example, a `HomePage` template that includes a `Header` (Organism), a `MainContent` (Organism), and a `Footer` (Organism).

This approach enhances scalability and maintainability of your code.

## Efficient Code Splitting, Dynamic Imports, and Loading of Client-Side Scripts

- Leverage Next.js's built-in code splitting to manage application code in smaller, more manageable units.
- Utilize dynamic imports for loading non-essential components on demand, reducing the initial bundle size and improving page load times.
- Identify components or code segments not critical for the initial page load and load them asynchronously.

Here's an example of how you can use `Next.js's` built-in code splitting and dynamic imports to load a non-essential component on demand:

```javascript
import dynamic from "next/dynamic";

// This component is loaded dynamically
const DynamicComponent = dynamic(() =>
  import("../components/DynamicComponent")
);

function MyComponent() {
  return (
    <div>
      <h1>Hello from MyComponent</h1>
      <DynamicComponent />
    </div>
  );
}

export default MyComponent;
```

In this example, `DynamicComponent` is not loaded until `MyComponent` is rendered. This can significantly improve performance by reducing the initial bundle size, especially if `DynamicComponent` is a large component or is not critical for the initial page load.

**Remember**: Dynamic imports returns a promise, so if you need to dynamically import a module inside a function, you would need to use `await`:

```javascript
async function fetchData() {
  const dynamicModule = await import("../modules/dynamicModule");
  const data = dynamicModule.fetchData();
  // ...
}
```

In this case, the dynamicModule is not loaded until the fetchData function is called.

## Rendering Modes

- **Server-Side Rendering (SSR)**: Use for pages requiring dynamic content or SEO, ideal for public-facing and content-heavy pages.

  Here's an example of Server-Side Rendering (SSR) in Next.js using the getServerSideProps function:

  ```javascript
  import fetch from "isomorphic-unfetch";

  function MyPage({ stars }) {
    return (
      <div>
        <p>Next.js has {stars} ⭐️ on GitHub!</p>
      </div>
    );
  }

  export async function getServerSideProps() {
    const res = await fetch("https://api.github.com/repos/vercel/next.js");
    const json = await res.json();
    return { props: { stars: json.stargazers_count } };
  }

  export default MyPage;
  ```

  In this example, `getServerSideProps` fetches the number of stars the Next.js repository has on GitHub. This data fetching happens on the server on each request. The fetched data is then passed to the `MyPage` component as a prop. This is ideal for pages that require dynamic content or SEO, as the page is always up-to-date on each request and the content is fully rendered before being sent to the client, making it crawlable by search engines.

- **Static Site Generation (SSG)**: Use for content with infrequent changes, suitable for landing pages, blogs, and marketing sites.
  Here's an example of Static Site Generation (SSG) in Next.js using the getStaticProps function:

  ```javascript
  import fetch from "isomorphic-unfetch";

  function MyPage({ stars }) {
    return (
      <div>
        <p>Next.js has {stars} ⭐️ on GitHub!</p>
      </div>
    );
  }

  export async function getStaticProps() {
    const res = await fetch("https://api.github.com/repos/vercel/next.js");
    const json = await res.json();
    return { props: { stars: json.stargazers_count } };
  }

  export default MyPage;
  ```

  In this example, `getStaticProps` fetches the number of stars the Next.js repository has on GitHub. This data fetching happens at build time. The fetched data is then passed to the `MyPage` component as a prop. This is ideal for pages that don't require dynamic content or have infrequent changes, as the page is pre-rendered at build time and served as a static HTML file. This can lead to faster page load times and better performance.

- **Client-Side Rendering (CSR)**: Use for interactive components and real-time data, prioritizing pages/components that require interactivity.
  In Next.js, Client-Side Rendering (CSR) can be achieved by using React state and effects in your components. Here's an example:

  ```javascript
  import { useState, useEffect } from "react";

  function MyPage() {
    const [data, setData] = useState(null);

    useEffect(() => {
      fetch("https://api.github.com/repos/vercel/next.js")
        .then((response) => response.json())
        .then((data) => setData(data));
    }, []);

    if (!data) {
      return <div>Loading...</div>;
    }

    return (
      <div>
        <p>Next.js has {data.stargazers_count} ⭐️ on GitHub!</p>
      </div>
    );
  }

  export default MyPage;
  ```

  In this example, the data is fetched on the client side after the component is mounted, using the `useEffect` hook. The fetched data is stored in the component's local state using the `useState` hook, and the component re-renders when the data is updated. This is a typical example of Client-Side Rendering (CSR) in Next.js.

- **Hybrid Rendering**: Combine SSR and CSR benefits for applications with mixed content and interactivity needs.
  In Next.js, you can achieve Hybrid Rendering by combining Static Site Generation (SSG), Server-Side Rendering (SSR), and Client-Side Rendering (CSR) in the same application. Here's an example:

  ```javascript
  // This page is statically generated at build time
  export default function HomePage({ serverRenderedPosts }) {
    const [clientRenderedTime, setClientRenderedTime] = useState(null);

    useEffect(() => {
      // This part is rendered on the client side
      setClientRenderedTime(new Date().toLocaleTimeString());
    }, []);

    return (
      <div>
        <h1>Home Page</h1>
        <p>This page was server-rendered at {serverRenderedPosts.date}.</p>
        <p>It was last client-rendered at {clientRenderedTime}.</p>
        <ul>
          {serverRenderedPosts.posts.map((post) => (
            <li key={post.id}>{post.title}</li>
          ))}
        </ul>
      </div>
    );
  }

  // This function runs at build time in production
  export async function getStaticProps() {
    const res = await fetch("https://jsonplaceholder.typicode.com/posts");
    const posts = await res.json();

    return {
      props: {
        serverRenderedPosts: {
          date: new Date().toLocaleTimeString(),
          posts: posts.slice(0, 5),
        },
      },
    };
  }
  ```

  In this example, `getStaticProps` fetches posts from an API at build time (SSG). The posts are then passed as props to the `HomePage` component. Inside the `HomePage` component, the `useEffect` hook sets the current time when the component is rendered on the client side (CSR). This way, the page benefits from both server-side and client-side rendering.

## Styling

- We always prefer using Tailwind CSS for styling in our projects.
- Use the `Image` component from `next/image` for optimized image handling.
- Use utility-first CSS frameworks like Tailwind CSS.

  ### Here's an example:

  ```javascript
  import Image from "next/image";

  export default function MyComponent() {
    return (
      <div className="flex items-center justify-center h-screen bg-gray-200">
        <div className="text-center">
          <Image
            src="/path/to/your/image.jpg"
            alt="Description of the image"
            width={500}
            height={300}
          />
          <h1 className="text-4xl font-bold text-blue-500">Hello, Next.js!</h1>
          <p className="text-xl text-blue-700">
            With Tailwind CSS and next/image
          </p>
        </div>
      </div>
    );
  }
  ```

  In this example, the `Image` component from `next/image` is used for optimized image handling, and Tailwind CSS utility classes are used for styling.

## Accessibility

- Follow Web Content Accessibility Guidelines (WCAG) to ensure your application is accessible.
- Use semantic HTML and ARIA attributes where necessary.
- Test accessibility with tools and aim for keyboard navigability and screen reader compatibility.

  ### Here's an example of a Next.js component that follows accessibility best practices:

  ```javascript
  import { useState } from "react";

  function AccessibleComponent() {
    const [count, setCount] = useState(0);

    return (
      <div>
        <h1 id="header" tabIndex="0">
          Accessible Component
        </h1>
        <button
          aria-label="Increment count"
          onClick={() => setCount(count + 1)}
        >
          Click me
        </button>
        <p aria-live="polite">Clicked {count} times</p>
        <a href="#header" aria-label="Back to header">
          Back to top
        </a>
      </div>
    );
  }

  export default AccessibleComponent;
  ```

  In this example:

  - The `h1` tag has a `tabIndex` of `0`, making it focusable with the keyboard.
  - The `button` has an `aria-label` attribute, providing a description for screen readers.
  - The `p` tag has an `aria-live` attribute, which helps screen readers understand that the content of the `p` tag will change when the button is clicked.
  - The `a` tag has an `aria-label` attribute, providing a description for screen readers.

  _These practices help ensure that the component is accessible according to the Web Content Accessibility Guidelines (WCAG)._

## Performance Optimization

- ### Optimize images and leverage Next.js's image optimization.

  ```javascript
  import Image from "next/image";

  export default function MyComponent() {
    return (
      <div>
        <Image
          src="/path/to/your/image.jpg"
          alt="Description of the image"
          width={500}
          height={300}
        />
        <h1>Hello, Next.js!</h1>
      </div>
    );
  }
  ```

- ### Use static generation (SSG) where possible for faster page loads.

  ```javascript
  export async function getStaticProps() {
    const res = await fetch("https://api.github.com/repos/vercel/next.js");
    const json = await res.json();
    return { props: { stars: json.stargazers_count } };
  }

  export default function MyPage({ stars }) {
    return (
      <div>
        <p>Next.js has {stars} ⭐️ on GitHub!</p>
      </div>
    );
  }
  ```

- ### Analyze and optimize bundle sizes with Next.js's `@next/bundle-analyzer` analytics.

  To use `@next/bundle-analyzer`, you need to install it and configure it in your Next.js project. Here are the steps::

  1. Install @next/bundle-analyzer and cross-env (used to set environment variables):

  ```
  npm install --save-dev @next/bundle-analyzer cross-env
  ```

  2. Create a `next.config.js` file in your project root (if you don't have one already) and require `@next/bundle-analyzer`. Set up a configuration function to enable the bundle analyzer when the `ANALYZE` environment variable is `true`:

  ```javascript
  const withBundleAnalyzer = require("@next/bundle-analyzer")({
    enabled: process.env.ANALYZE === "true",
  });

  module.exports = withBundleAnalyzer({});
  ```

  3. Add an `analyze` script to your `package.json` file to build your application and start the bundle analyzer:

  ```json
    "scripts": {
        "dev": "next dev",
        "build": "next build",
        "start": "next start",
        "analyze": "cross-env ANALYZE=true next build"
    }
  ```

## Security Practices

- Sanitize user input to prevent XSS attacks.
- Use environment variables for sensitive information.
- Regularly update dependencies to mitigate vulnerabilities.

## Testing

- Write unit and integration tests using tools like Jest and React Testing Library.
- Aim for a high coverage to ensure reliability and maintainability.

## Documentation

- Document components and functions clearly, providing examples where necessary.
- Maintain a README file with project setup instructions and other essential information.

By adhering to these guidelines, developers can ensure that their Next.js projects are well-organized, maintainable, and scalable. Adapt these recommendations based on specific project needs and preferences.
