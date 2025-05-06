---
source-updated-at: 2025-03-07T12:34:19.000Z
translation-updated-at: 2025-04-05T03:26:00.000Z
title: 自定义链接
---

虽然在许多情况下重复代码是可以接受的，但您可能会发现自己过于频繁地这样做。有时，您可能希望创建具有额外行为或样式的横切组件。您也可以考虑将第三方库与 TanStack Router 的类型安全功能结合使用。

## `createLink` 用于横切关注点

`createLink` 创建一个与 `Link` 具有相同类型参数的自定义 `Link` 组件。这意味着您可以创建自己的组件，提供与 `Link` 相同的类型安全和 TypeScript 性能。

### 基础示例

如果您想创建一个基础的自定义链接组件，可以按照以下方式实现：

[//]: # 'BasicExampleImplementation'

```tsx
import * as React from 'react'
import { createLink, LinkComponent } from '@tanstack/react-router'

interface BasicLinkProps extends React.AnchorHTMLAttributes<HTMLAnchorElement> {
  // 添加您希望传递给锚点元素的任何额外属性
}

const BasicLinkComponent = React.forwardRef<HTMLAnchorElement, BasicLinkProps>(
  (props, ref) => {
    return (
      <a ref={ref} {...props} className={'block px-3 py-2 text-blue-700'} />
    )
  },
)

const CreatedLinkComponent = createLink(BasicLinkComponent)

export const CustomLink: LinkComponent<typeof BasicLinkComponent> = (props) => {
  return <CreatedLinkComponent preload={'intent'} {...props} />
}
```

[//]: # 'BasicExampleImplementation'

然后，您可以像使用其他 `Link` 组件一样使用新创建的 `Link` 组件：

```tsx
<CustomLink to={'/dashboard/invoices/$invoiceId'} params={{ invoiceId: 0 }} />
```

[//]: # 'ExamplesUsingThirdPartyLibs'

## 与第三方库一起使用 `createLink`

以下是一些如何使用 `createLink` 与第三方库的示例。

### React Aria Components 示例

React Aria Components 的
[Link](https://react-spectrum.adobe.com/react-aria/Link.html) 组件不支持标准的 `onMouseEnter` 和 `onMouseLeave` 事件。
因此，您不能直接将其与 TanStack Router 的 `preload (intent)` 属性一起使用。

相关解释可在此处找到：

- [https://react-spectrum.adobe.com/react-aria/interactions.html](https://react-spectrum.adobe.com/react-aria/interactions.html)
- [https://react-spectrum.adobe.com/blog/building-a-button-part-2.html](https://react-spectrum.adobe.com/blog/building-a-button-part-2.html)

可以通过使用 [React Aria Hooks](https://react-spectrum.adobe.com/react-aria/hooks.html) 中的 [useLink](https://react-spectrum.adobe.com/react-aria/useLink.html) 钩子与标准锚点元素来解决此问题。

```tsx
import * as React from 'react'
import { createLink, LinkComponent } from '@tanstack/react-router'
import {
  mergeProps,
  useFocusRing,
  useHover,
  useLink,
  useObjectRef,
} from 'react-aria'
import type { AriaLinkOptions } from 'react-aria'

interface RACLinkProps extends Omit<AriaLinkOptions, 'href'> {
  children?: React.ReactNode
}

const RACLinkComponent = React.forwardRef<HTMLAnchorElement, RACLinkProps>(
  (props, forwardedRef) => {
    const ref = useObjectRef(forwardedRef)

    const { isPressed, linkProps } = useLink(props, ref)
    const { isHovered, hoverProps } = useHover(props)
    const { isFocusVisible, isFocused, focusProps } = useFocusRing(props)

    return (
      <a
        {...mergeProps(linkProps, hoverProps, focusProps, props)}
        ref={ref}
        data-hovered={isHovered || undefined}
        data-pressed={isPressed || undefined}
        data-focus-visible={isFocusVisible || undefined}
        data-focused={isFocused || undefined}
      />
    )
  },
)

const CreatedLinkComponent = createLink(RACLinkComponent)

export const CustomLink: LinkComponent<typeof RACLinkComponent> = (props) => {
  return <CreatedLinkComponent preload={'intent'} {...props} />
}
```

### Chakra UI 示例

```tsx
import * as React from 'react'
import { createLink, LinkComponent } from '@tanstack/react-router'
import { Link } from '@chakra-ui/react'

interface ChakraLinkProps
  extends Omit<React.ComponentPropsWithoutRef<typeof Link>, 'href'> {
  // 添加您希望传递给链接的任何额外属性
}

const ChakraLinkComponent = React.forwardRef<
  HTMLAnchorElement,
  ChakraLinkProps
>((props, ref) => {
  return <Link ref={ref} {...props} />
})

const CreatedLinkComponent = createLink(ChakraLinkComponent)

export const CustomLink: LinkComponent<typeof ChakraLinkComponent> = (
  props,
) => {
  return (
    <CreatedLinkComponent
      textDecoration={'underline'}
      _hover={{ textDecoration: 'none' }}
      _focus={{ textDecoration: 'none' }}
      preload={'intent'}
      {...props}
    />
  )
}
```

### MUI 示例

这里有一个[示例](../examples/start-material-ui)使用了这些模式。

#### `Link`

如果 MUI `Link` 只需要像路由器 `Link` 一样行为，可以直接用 `createLink` 包装：

```tsx
import { createLink } from '@tanstack/react-router'
import { Link } from '@mui/material'

export const CustomLink = createLink(Link)
```

如果需要自定义 `Link`，可以使用以下方法：

```tsx
import React from 'react'
import { createLink } from '@tanstack/react-router'
import { Link } from '@mui/material'
import type { LinkProps } from '@mui/material'
import type { LinkComponent } from '@tanstack/react-router'

interface MUILinkProps extends LinkProps {
  // 添加您希望传递给 Link 的任何额外属性
}

const MUILinkComponent = React.forwardRef<HTMLAnchorElement, MUILinkProps>(
  (props, ref) => <Link ref={ref} {...props} />,
)

const CreatedLinkComponent = createLink(MUILinkComponent)

export const CustomLink: LinkComponent<typeof MUILinkComponent> = (props) => {
  return <CreatedLinkComponent preload={'intent'} {...props} />
}

// 也可以进行样式化
```

#### `Button`

如果要将 `Button` 用作路由器 `Link`，应将 `component` 设置为 `a`：

```tsx
import React from 'react'
import { createLink } from '@tanstack/react-router'
import { Button } from '@mui/material'
import type { ButtonProps } from '@mui/material'
import type { LinkComponent } from '@tanstack/react-router'

interface MUIButtonLinkProps extends ButtonProps<'a'> {
  // 添加您希望传递给 Button 的任何额外属性
}

const MUIButtonLinkComponent = React.forwardRef<
  HTMLAnchorElement,
  MUIButtonLinkProps
>((props, ref) => <Button ref={ref} component="a" {...props} />)

const CreatedButtonLinkComponent = createLink(MUIButtonLinkComponent)

export const CustomButtonLink: LinkComponent<typeof MUIButtonLinkComponent> = (
  props,
) => {
  return <CreatedButtonLinkComponent preload={'intent'} {...props} />
}
```

#### 与 `styled` 一起使用

这些 MUI 方法都可以与 `styled` 一起使用：

```tsx
import { css, styled } from '@mui/material'
import { CustomLink } from './CustomLink'

const StyledCustomLink = styled(CustomLink)(
  ({ theme }) => css`
    color: ${theme.palette.common.white};
  `,
)
```

### Mantine 示例

```tsx
import * as React from 'react'
import { createLink, LinkComponent } from '@tanstack/react-router'
import { Anchor, AnchorProps } from '@mantine/core'

interface MantineAnchorProps extends Omit<AnchorProps, 'href'> {
  // 添加您希望传递给锚点的任何额外属性
}

const MantineLinkComponent = React.forwardRef<
  HTMLAnchorElement,
  MantineAnchorProps
>((props, ref) => {
  return <Anchor ref={ref} {...props} />
})

const CreatedLinkComponent = createLink(MantineLinkComponent)

export const CustomLink: LinkComponent<typeof MantineLinkComponent> = (
  props,
) => {
  return <CreatedLinkComponent preload="intent" {...props} />
}
```

[//]: # 'ExamplesUsingThirdPartyLibs'

---

注意：所有代码块、URL、文件路径和变量名称均保持原样未翻译。
