---
source-updated-at: '2025-03-07T12:34:19.000Z'
translation-updated-at: '2025-05-08T20:20:50.406Z'
title: 自訂連結
---

雖然在許多情況下重複使用程式碼是可以接受的，但您可能會發現自己過於頻繁地這樣做。有時，您可能希望建立具有額外行為或樣式的橫切關注點 (cross-cutting) 元件。您也可以考慮將第三方函式庫與 TanStack Router 的類型安全 (type safety) 結合使用。

## `createLink` 用於橫切關注點

`createLink` 會建立一個與 `Link` 具有相同類型參數的自訂 `Link` 元件。這意味著您可以建立自己的元件，提供與 `Link` 相同的類型安全和 TypeScript 效能。

### 基礎範例

如果您想建立一個基本的自訂連結元件，可以按照以下方式進行：

[//]: # 'BasicExampleImplementation'

```tsx
import * as React from 'react'
import { createLink, LinkComponent } from '@tanstack/react-router'

interface BasicLinkProps extends React.AnchorHTMLAttributes<HTMLAnchorElement> {
  // 新增您想傳遞給錨點元素的任何額外 props
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

然後您可以像使用其他 `Link` 元件一樣使用新建立的 `Link` 元件：

```tsx
<CustomLink to={'/dashboard/invoices/$invoiceId'} params={{ invoiceId: 0 }} />
```

[//]: # 'ExamplesUsingThirdPartyLibs'

## 與第三方函式庫一起使用 `createLink`

以下是一些如何與第三方函式庫一起使用 `createLink` 的範例。

### React Aria Components 範例

React Aria Components 的
[Link](https://react-spectrum.adobe.com/react-aria/Link.html) 元件不支援標準的 `onMouseEnter` 和 `onMouseLeave` 事件。
因此，您無法直接將其與 TanStack Router 的 `preload (intent)` prop 一起使用。

相關說明可以在這裡找到：

- [https://react-spectrum.adobe.com/react-aria/interactions.html](https://react-spectrum.adobe.com/react-aria/interactions.html)
- [https://react-spectrum.adobe.com/blog/building-a-button-part-2.html](https://react-spectrum.adobe.com/blog/building-a-button-part-2.html)

可以通過使用 [React Aria Hooks](https://react-spectrum.adobe.com/react-aria/hooks.html) 中的 [useLink](https://react-spectrum.adobe.com/react-aria/useLink.html) 鉤子 (hook) 與標準錨點元素來解決這個問題。

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

### Chakra UI 範例

```tsx
import * as React from 'react'
import { createLink, LinkComponent } from '@tanstack/react-router'
import { Link } from '@chakra-ui/react'

interface ChakraLinkProps
  extends Omit<React.ComponentPropsWithoutRef<typeof Link>, 'href'> {
  // 新增您想傳遞給連結的任何額外 props
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

### MUI 範例

有一個可用的[範例](../examples/start-material-ui)使用了這些模式。

#### `Link`

如果 MUI `Link` 應該像路由器的 `Link` 一樣行為，可以直接用 `createLink` 包裝：

```tsx
import { createLink } from '@tanstack/react-router'
import { Link } from '@mui/material'

export const CustomLink = createLink(Link)
```

如果需要自訂 `Link`，可以使用這種方法：

```tsx
import React from 'react'
import { createLink } from '@tanstack/react-router'
import { Link } from '@mui/material'
import type { LinkProps } from '@mui/material'
import type { LinkComponent } from '@tanstack/react-router'

interface MUILinkProps extends LinkProps {
  // 新增您想傳遞給 Link 的任何額外 props
}

const MUILinkComponent = React.forwardRef<HTMLAnchorElement, MUILinkProps>(
  (props, ref) => <Link ref={ref} {...props} />,
)

const CreatedLinkComponent = createLink(MUILinkComponent)

export const CustomLink: LinkComponent<typeof MUILinkComponent> = (props) => {
  return <CreatedLinkComponent preload={'intent'} {...props} />
}

// 也可以進行樣式設定
```

#### `Button`

如果 `Button` 應該用作路由器的 `Link`，應將 `component` 設為 `a`：

```tsx
import React from 'react'
import { createLink } from '@tanstack/react-router'
import { Button } from '@mui/material'
import type { ButtonProps } from '@mui/material'
import type { LinkComponent } from '@tanstack/react-router'

interface MUIButtonLinkProps extends ButtonProps<'a'> {
  // 新增您想傳遞給 Button 的任何額外 props
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

#### 與 `styled` 一起使用

這些 MUI 方法都可以與 `styled` 一起使用：

```tsx
import { css, styled } from '@mui/material'
import { CustomLink } from './CustomLink'

const StyledCustomLink = styled(CustomLink)(
  ({ theme }) => css`
    color: ${theme.palette.common.white};
  `,
)
```

### Mantine 範例

```tsx
import * as React from 'react'
import { createLink, LinkComponent } from '@tanstack/react-router'
import { Anchor, AnchorProps } from '@mantine/core'

interface MantineAnchorProps extends Omit<AnchorProps, 'href'> {
  // 新增您想傳遞給錨點的任何額外 props
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
