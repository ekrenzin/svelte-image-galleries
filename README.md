# Svelte Image Galleries

## Description

This package is a performant image gallery

## Installation

Install the package using npm:

```bash
npm install your-package-name
```

# usage example

```svelte
<script lang="ts">
	import Bubbles from 'svelte-image-galleries';

	const numberOfImages = 200;

	let imageSources: string[] = new Array(numberOfImages)
		.fill(0)
		.map((_, i) => `https://picsum.photos/200/200?random=${i}`);
	let mode = 'bounce';
</script>

<Bubbles {imageSources} range={200} radius={100} borderRadius={10} mode="bounce" />
```

# Live Demo

https://svelte-image-galleries.pages.dev
