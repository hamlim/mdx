# Exports

#### `export default`

The ES default [export][] is used to provide a layout component which will wrap
the transpiled JSX.

You can export it as a function:

```jsx
import Layout from './Layout'

export default ({ children }) => <Layout some='metadata' >{children}</Layout>

# Hello, world!
```

Or directly as a component:

```jsx
import Layout from './Layout'

export default Layout

# Hello, world!
```

[export]: https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/export
