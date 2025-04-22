
## 1. save page state

```vue
<NuxtPage keepalive />
```

```vue
<template>
	<div>
		Keepin it alive.
		<NuxtPage />
	</div>
</template>
<script setup>
definePageMeta({
	keepalive: true,
});
</script>
```

## 2. devonly component

```vue
<template>
	<div>
		<DevOnly>
			dev mode
		</DevOnly>
	</div>
</template>
```

## 3. production component

```vue
	<DevOnly>
		<DevAccountSwitcher />
		<template #fallback>
		<div>This is rendered only in the production build.</div>
	</DevOnly>
```

## 4. client only component

```vue
<template>
	<div>
		<p>A regular component rendered on the server and client.</p>
		<ClientOnly>
			<p>But this part shouldn't be rendered on the server</p>
			<WillBreakOnTheServer />
		</ClientOnly>
	</div>
</template>
```

loading status

```vue
<template>
	<div>
	<p>A regular component rendered on the server and client.</p>
	<ClientOnly>
		<p>But this part shouldn't be rendered on the server</p>
		<WillBreakOnTheServer />
		<template #fallback>
			<Spinner>
				Just give me a moment while I load some things.
			</Spinner>
		</template>
	</ClientOnly>
</div>
</template>
```

 ### \*\.client.vue
 - they must be auto-imported or imported manually through #components .
 - we have to wait a tick before accessing the template.

```vue
<template>
	<div ref="container">
	<!-- Do some cool stuff here -->
	</div>
</template>
<script setup>
const container = ref(null);
onMounted(async () => {
// Nothing has been rendered yet
console.log(container.value); // -> null
// Wait one tick for the render
await nextTick();
// Now we can access it!
console.log(container.value) // -> <div ...>
};
</script>
```

## 5. Lazy Loading components

```vue
<!-- Loads as soon as possible -->
<Modal v-if="showModal" />
<!-- Only loads when showModal = true -->
<LazyModal v-if="showModal" />
```

## 6. Global Components

- By default, all components inside of ~/components/global will be auto-imported and made global.
- You can also make any component global by adding the \*\.global.vue suffix.

```ts
export default defineNuxtConfig({
components: [
	// Keep the default component folder
	'~/components',
	// Add in a custom global folder,
	{
		path: '~/globalComponents',
		global: true,
	},
]
});
```

## 7. NuxtLink Basics

```vue
<NuxtLink
to="www.masteringnuxt.com"
external
noopener
noreferrer
nofollow
>
Mastering Nuxt
</NuxtLink>
```

- noopener: The newly opened page cannot obtain the window object of the source page.
- noreferrer: Prevent referrer info from being passed to the target site by removing the referrer in http.
- nofollow: Used to tell search engines not to follow specific web links.

## 8. Use NuxtLink to open links in a new tab

open links in a new tab

```vue
<NuxtLink
to="/articles"
target="_blank"
>
Mastering Nuxt 3
</NuxtLink>
```

## 9. Prefetch Pages with NuxtLink

prefetch

```vue
<NuxtLink to="/articles" prefetch>Articles</NuxtLink>
```

no prefetch

```vue
<NuxtLink to="/articles" no-prefetch>Articles</NuxtLink>
```

## 10. Custom NuxtLink Component

```ts
defineNuxtLink({
componentName?: string;
externalRelAttribute?: string;
activeClass?: string;
exactActiveClass?: string;
prefetchedClass?: string;
trailingSlash?: 'append' | 'remove'
}) => Component
```

## 11. Layout Components

define layout

```vue
<NuxtLayout name="blogPost">
<NuxtPage />
</NuxtLayout>
```

use layout

```vue
definePageMeta({
layout: false,
});

--or--

definePageMeta({
layout: 'blogPost'
})
```

```md
---
layout: blogPost
---
# All About Layouts
```

## 12. Layout fallbacks

```vue
<NuxtLayout fallback="differentDefault">
    <NuxtPage />
</NuxtLayout>
```

Normally, the NuxtLayout component will use the default layout if no other layout is
specified — either through definePageMeta , setPageLayout , or directly on the
NuxtLayout component itself.


## 13. Dynamic Layouts

```vue
const layout = ref('blog');
const toggleLayout = () => {
// Toggle between 'blog' and 'blog-condensed'
if (layout.value === 'blog') {
layout.value = 'blog-condensed';
} else {
layout.value = 'blog';
}
setPageLayout(layout.value);
};
```

Which can also be used in route middleware:

```vue
export default defineNuxtRouteMiddleware((to) => {
// Use condensed layout, eg ?condensed=true
if (to.query.condensed === 'true') {
setPageLayout('condensed');
}
});
```