<script lang="ts">
	import Bubbles from '$lib/Bubbles.svelte';

	const numberOfImages = 200;

	let imageSources: string[] = new Array(numberOfImages)
		.fill(0)
		.map((_, i) => `https://picsum.photos/200/200?random=${i}`);
	let mode = 'bounce';
	const script = `
<script lang="ts">\n
	import Bubbles from 'svelte-image-galleries';\n

	const numberOfImages = 200;

	let imageSources: string[] = new Array(numberOfImages)
		.fill(0)
		.map((_, i) => \`https://picsum.photos/200/200?random=\${i}\`);
	let mode = 'bounce';
\<\/script>


<Bubbles {imageSources} range={200} radius={100} borderRadius={10} mode="bounce" />`;
</script>

<main>
	<h1>Svelte Bubble Images Gallery</h1>
	<h2>Performant image gallery using html canvas</h2>
	<p>
		This demo uses 1000 images loaded from the{' '}
		<a href="https://picsum.photos/" target="_blank" rel="noopener"> Picsum Photos </a>{' '}
		API.
	</p>
	<ul>
		<li>Range - The distance the images will travel each animation.</li>
		<li>Radius - The size of the image bubble.</li>
		<li>Number of Images - The number of images to load.</li>
		<li>Border Radius - The border radius of the image bubble.</li>
		<li>Mode - The mode of the animation, either bounce or wrap. 'bounce' | 'wrap' | 'gravity'</li>
	</ul>
	<pre>
		<code>
		{script}
	</code>
</pre>
	<select bind:value={mode}>
		<option value="bounce">Bounce</option>
		<option value="wrap">Wrap</option>
		<option value="gravity">Gravity</option>
	</select>
	<div class="modes">
		<div class="bubble-wrapper">
			{#key mode}
				<Bubbles {imageSources} range={200} radius={100} borderRadius={10} {mode} />
			{/key}
		</div>
	</div>
</main>

<style>
	main {
		background-color: #fff;
		display: flex;
		flex-direction: column;
		align-items: center;
		height: 100vh;
		width: 100vw;
		position: fixed;
		top: 0;
		left: 0;
		overflow: auto;
		color: #1d1b1b;
		font-family: sans-serif;
	}

	.modes {
		display: grid;
		gap: 20px; /* Spacing between items */
		padding: 20px; /* Padding around the grid */
	}

	h2 {
		font-weight: 100;
	}

	a {
		color: #000;
	}

	.bubble-wrapper {
		display: flex;
		justify-content: center;
		align-items: center;
		overflow: hidden;
		margin: auto;
		max-height: 80vh;
		border: 0.5rem solid #000;
	}

	pre {
		background-color: #ccc;
		padding: 1rem;
	}
</style>
