---
source-updated-at: '2025-02-22T09:47:51.000Z'
translation-updated-at: '2025-04-05T21:27:24.000Z'
ref: docs/router/zh-hans/framework/react/guide/custom-link.md
replace:
  react-router: solid-router
---

[//]: # 'BasicExampleImplementation'

```tsx
import * as Solid from 'solid-js'
import { createLink, LinkComponent } from '@tanstack/solid-router'

export const Route = createRootRoute({
  component: RootComponent,
})

type BasicLinkProps = Solid.JSX.IntrinsicElements['a'] & {
  // Add any additional props you want to pass to the anchor element
}

const BasicLinkComponent: Solid.Component<BasicLinkProps> = (props) => (
  <a {...props} class="block px-3 py-2 text-red-700">
    {props.children}
  </a>
)

const CreatedLinkComponent = createLink(BasicLinkComponent)

export const CustomLink: LinkComponent<typeof BasicLinkComponent> = (props) => {
  return <CreatedLinkComponent preload={'intent'} {...props} />
}
```

[//]: # 'BasicExampleImplementation'
[//]: # 'ExamplesUsingThirdPartyLibs'

## `createLink` with third party libraries

Here are some examples of how you can use `createLink` with third-party libraries.

### Some Library example

```tsx
// TODO: Add this example.
```

[//]: # 'ExamplesUsingThirdPartyLibs'
