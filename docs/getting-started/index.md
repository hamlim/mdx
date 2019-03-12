import { Flex, Card, Link, Text } from 'rebass'

export const InstallationGuides = () => (
  <Flex flexWrap="wrap">
    <Card mb={3} mr={3} px={4} py={3} width={1/2.1}>
      <Text fontWeight="bold" textAlign="center">
        <Link color="black" href="/getting-started/next">Next.js</Link>
      </Text>
    </Card>
    <Card mb={3} px={4} py={3} width={1/2.1}>
      <Text fontWeight="bold" textAlign="center">
        <Link color="black" href="/getting-started/gatsby">Gatsby</Link>
      </Text>
    </Card>
    <Card mb={3} mr={3} px={4} py={3} width={1/2.1}>
      <Text fontWeight="bold" textAlign="center">
        <Link color="black" href="/getting-started/create-react-app">Create React App</Link>
      </Text>
    </Card>
    <Card mb={3} px={4} py={3} width={1/2.1}>
      <Text fontWeight="bold" textAlign="center">
        <Link color="black" href="/getting-started/react-static">React Static</Link>
      </Text>
    </Card>
    <Card mb={3} mr={3} px={4} py={3} width={1/2.1}>
      <Text fontWeight="bold" textAlign="center">
        <Link color="black" href="/getting-started/wepack">Webpack</Link>
      </Text>
    </Card>
    <Card mb={3} px={4} py={3} width={1/2.1}>
      <Text fontWeight="bold" textAlign="center">
        <Link color="black" href="/getting-started/parcel">Parcel</Link>
      </Text>
    </Card>
  </Flex>
)

# Getting Started

If you have an existing project you want to integrate MDX with, check out
the installation guides.

<InstallationGuides />

## Table of contents

*   [Syntax](#syntax)
    *   [Markdown](#markdown)
    *   [JSX](#jsx)
    *   [Exports](#exports)
*   [Working with components](#working-with-components)
    *   [MDXProvider](#mdxprovider)
    *   [Table of components](#table-of-components)
*   [Installation guides](#installation-guides)
*   [Scaffold out an app](#scaffold-out-an-app)

## Syntax

MDX syntax can be boiled down to being JSX in Markdown.  It’s a superset of
Markdown syntax that also supports importing, exporting, and JSX.

### Markdown

Standard [Markdown syntax][md] is supported.  It’s recommended to learn about
Markdown in their [docs][md].

### JSX

[JSX syntax][jsx] is fully supported, JSX blocks are opened by starting a line
with the `<` character.

```.mdx
# Below is a JSX block

<div style={{ padding: '10px 30px', backgroundColor: 'tomato' }}>
  <h2>Try making the h2 have the color green</h2>
</div>
```

#### Imports

Imports can be used to [import][] components into the scope and later rendered:

```jsx
import { Box, Heading, Text } from 'rebass'

# Here is a JSX block

It is using imported components!

<Box>
  <Heading>Here's a JSX block</Heading>
  <Text>It's pretty neat</Text>
</Box>
```

You can also import data that you want to display in a JSX block:

```mdx
import snowfallData from './snowfall.json'
import BarChart from './charts/BarChart'

# Recent snowfall trends

2019 has been a particularly snowy year when compared to the last decade

<BarChart data={snowfallData} />
```

##### Embedding documents

You can embed MDX documents in other documents.  This is also known as
[transclusion][transclude].  You can achieve this by importing one `.md`
or `.mdx` file into another:

```jsx
import License from './license.md'
import Contributing from './docs/contributing.md'

# Hello, world!

<License />

---

<Contributing />
```

### Exports

You can use exports to export metadata like layout or authors.  It’s a mechanism for
an imported MDX file to communicate with its parent.  It works similarly to frontmatter,
but uses ES2015 syntax.

```js
// posts/post.mdx
import { fred, sue } from '../data/authors'
import Layout from '../components/blog-layout'

export const metadata = {
  authors: [fred, sue],
  title: 'My awesome article',
  layout: Layout
}

# Post about MDX

MDX is a JSX in Markdown loader, parser, and renderer for ambitious projects.
```

Those exports can then be imported by another file:

```jsx
// index.js
import React from 'react'
import MDXDocument, { metadata } from 'posts/post.mdx'

export default () => (
  <>
    <h3>{metadata.title}</h3>
    By: {metadata.authors.map(author => author.name).join(', ')}

    <MDXDocument />
  </>
)
```

[Learn more about exports](/advanced/exports)

## Working with components

MDX documents can be passed the components that they should render for
a given Markdown element.  The component mapping is based on the HTML
element that Markdown renders.  So, for `# I'm a heading` you would
specify `h1` because that’s the HTML element that it renders to.

Here’s a full example:

```jsx
// src/App.js
import React from 'react'

// Import the MDX document
import Hello from '../hello.mdx'

// Create a few components with styling
const MyH1 = props => <h1 style={{ color: 'tomato' }} {...props} />
const MyParagraph = props => <p style={{ fontSize: '18px', lineHeight: 1.6 }} />
const InlineCode = props => <code style={{ backgroundColor: 'silver' }} {...props} />

// Create your component mapping for Markdown elements
const components = {
  h1: MyH1,
  p: MyParagraph,
  inlineCode: Code
}

export default () => <Hello components={components} />
```

In `hello.mdx`:

```md
# This will be rendered with Heading

This will be rendered with Text, and `this rendered with inlineCode`
```

You can also import your components from another location like your UI library:

```jsx
// src/App.js
import React from 'react'
import Hello from '../hello.mdx'

import {
  Text,
  Heading,
  InlineCode
} from '../ui-library'

export default () =>
  <Hello
    components={{
      h1: Heading,
      p: Text,
      inlineCode: InlineCode
    }}
  />
```

### MDXProvider

If you’re using an app layout that wraps your application, you can use
the `MDXProvider` to pass your components in one place:

```jsx
// src/App.js
import React from 'react'
import { MDXProvider } from '@mdx-js/tag'

// Import components from your component library
import { Heading, Text, Code, Table } from './components'

// Import MDX documents from your project
import Doc1 from './content/doc-1.mdx'
import Doc2 from './content/doc-2.mdx'
import Doc3 from './content/doc-3.mdx'

const components = {
  h1: Heading.H1,
  h2: Heading.H2,
  p: Text,
  code: Pre,
  table: Table,
  inlineCode: Code
}

export default props =>
  <MDXProvider components={components}>
    <Doc1 />
    <Doc2 />
    <Doc3 />
  </MDXProvider>
```

All three MDX files will be rendered with the same components without
having to pass the components manually to each document.

### Table of components

MDXProvider uses [React Context][context] to provide the component mapping
internally to MDX when it renders.  The following components are rendered from
Markdown, so these can be keys in the component object you pass to MDXProvider.

| Tag             | Name                                                                 | Syntax                                              |
| --------------- | -------------------------------------------------------------------- | --------------------------------------------------- |
| `p`             | [Paragraph](https://github.com/syntax-tree/mdast#paragraph)          |                                                     |
| `h1`            | [Heading 1](https://github.com/syntax-tree/mdast#heading)            | `#`                                                 |
| `h2`            | [Heading 2](https://github.com/syntax-tree/mdast#heading)            | `##`                                                |
| `h3`            | [Heading 3](https://github.com/syntax-tree/mdast#heading)            | `###`                                               |
| `h4`            | [Heading 4](https://github.com/syntax-tree/mdast#heading)            | `####`                                              |
| `h5`            | [Heading 5](https://github.com/syntax-tree/mdast#heading)            | `#####`                                             |
| `h6`            | [Heading 6](https://github.com/syntax-tree/mdast#heading)            | `######`                                            |
| `thematicBreak` | [Thematic break](https://github.com/syntax-tree/mdast#thematicbreak) | `***`                                               |
| `blockquote`    | [Blockquote](https://github.com/syntax-tree/mdast#blockquote)        | `>`                                                 |
| `ul`            | [List](https://github.com/syntax-tree/mdast#list)                    | `-`                                                 |
| `ol`            | [Ordered list](https://github.com/syntax-tree/mdast#list)            | `1.`                                                |
| `li`            | [List item](https://github.com/syntax-tree/mdast#listitem)           |                                                     |
| `table`         | [Table](https://github.com/syntax-tree/mdast#table)                  | `--- | --- | ---`                                   |
| `tr`            | [Table row](https://github.com/syntax-tree/mdast#tablerow)           | `This | is | a | table row`                         |
| `td`/`th`       | [Table cell](https://github.com/syntax-tree/mdast#tablecell)         |                                                     |
| `pre`           | [Pre](https://github.com/syntax-tree/mdast#code)                     |                                                     |
| `code`          | [Code](https://github.com/syntax-tree/mdast#code)                    |                                                     |
| `em`            | [Emphasis](https://github.com/syntax-tree/mdast#emphasis)            | `_emphasis_`                                        |
| `strong`        | [Strong](https://github.com/syntax-tree/mdast#strong)                | `**strong**`                                        |
| `delete`        | [Delete](https://github.com/syntax-tree/mdast#delete)                | `~~strikethrough~~`                                 |
| `code`          | [InlineCode](https://github.com/syntax-tree/mdast#inlinecode)        |                                                     |
| `hr`            | [Break](https://github.com/syntax-tree/mdast#break)                  | `---`                                               |
| `a`             | [Link](https://github.com/syntax-tree/mdast#link)                    | `<https://mdxjs.com>` or `[MDX](https://mdxjs.com)` |
| `img`           | [Image](https://github.com/syntax-tree/mdast#image)                  | `![alt](https://mdx-logo.now.sh)`                   |

## Installation guides

Now that we’ve gone over how MDX works, you’re ready to get integrate it with
your project.  Choose the framework or tool that you’re using:

<InstallationGuides />

## Scaffold out an app

If you’re the type of person that wants to scaffold out an app quickly and start
playing around you can use `npm init`.  You can choose any of the commands below
to scaffold out a project to play around with:

*   `npm init mdx` [`parcel`](./parcel)
*   `npm init mdx` [`next`](./next)
*   `npm init mdx` [`create-react-app`](./create-react-app)
*   `npm init mdx` [`gatsby`](./gatsby)
*   `npm init mdx` [`x0`](./x0)
*   `npm init mdx` [`react-static`](./react-static)

[md]: https://daringfireball.net/projects/markdown/syntax

[jsx]: https://reactjs.org/docs/introducing-jsx.html

[import]: https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/import

[transclude]: https://en.wikipedia.org/wiki/Transclusion

[context]: https://reactjs.org/docs/context.html
