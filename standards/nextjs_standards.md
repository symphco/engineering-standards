# Nextjs Coding Standards

## File Structure & Naming Convention
- TBC

## Efficient Code Splitting, Dynamic Imports, and Loading of Client-Side Scripts
Leverage Next.js built-in code splitting to break down application code into smaller, more manageable units
  - Separate larger components or chunks of code into smaller, reusable modules
  - Breaking down components into smaller modules improves code organization and helps facilitate better maintenance and reusability

Utilize Dynamic Imports
  - Use dynamic imports to load non-essential components on demand.
  - By loading components dynamically, this reduces the initial bundle size and helps improve the overall page load times, especially for larger applications
  - Identify components or code segments that are not critical for the initial page load and load them asynchronously

## Identify which Rendering mode to use
Server Side Rendering (SSR)
  - Use for pages needing dynamic content or SEO
  - Preferrable for public-facing and content-heavy pages

Static Site Generation (SSG)
  - Use for content with infrequent changes
  - Prefer for landing pages, blogs, and marketing sites

Client-side Rendering (CSR)
  - Use for interactive components and real-time data
  - Prefer for pages/components which prioritze interactivity

Hybrid Rendering
  - Use to combine SSR and CSR benefits
  - Prefer for applications with mixed content and interactivity
