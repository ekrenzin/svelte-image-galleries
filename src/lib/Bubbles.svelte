<script lang="ts">
	import { onMount } from 'svelte';

	export let imageSources: string[] = [];
	export let range: number = 200; // Maximum offset from the origin
	export let radius = 100;
	export let borderRadius = 50;
	export let mode: 'bounce' | 'wrap' | 'gravity' = 'bounce';

	let images: BubbleImage[] = [];
	let size: { width: number; height: number } = { width: 2000, height: 2000 };
	let canvas: HTMLCanvasElement;
	let originX: number, originY: number;

	onMount(() => {
		originX = size.width / 2 - radius / 2;
		originY = size.height / 2 - radius / 2;
		loadImages();
	});

	function sizeCanvas(node: HTMLDivElement) {
		if (!node) return;
		const { width, height } = node.getBoundingClientRect();
		size.width = width;
		size.height = height;
	}

	function loadImages() {
		imageSources.forEach((src) => {
			const imageElement = new Image();
			imageElement.onload = () => {
				const img: BubbleImage = {
					id: Math.random() * 1000, // Unique identifier for the image
					src: src, // Source URL of the image
					element: imageElement, // The image element
					x: originX, // Initial x position
					y: originY, // Initial y position
					dx: 0, // Initial horizontal movement offset
					dy: 0, // Initial vertical movement offset
					radius: radius // Radius of the image bubble
				};
				images.push(img); // Add the loaded image to the images array
				// Start animation for this specific image
			};
			imageElement.onerror = () => {
				console.error(`Failed to load image from ${src}`);
			};
			imageElement.src = src; // Set the source which begins loading the image
		});
		startAnimation();
	}

	function startAnimation() {
		const ctx = canvas.getContext('2d');
		if (!ctx) return;
		ctx.clearRect(0, 0, canvas.width, canvas.height);

		images.forEach((image) => {
			// Generate new random directions if almost stationary
			if (Math.abs(image.dx) < 0.1 && Math.abs(image.dy) < 0.1) {
				const angle = Math.random() * 2 * Math.PI; // Random angle
				image.dx = Math.random() * range * Math.cos(angle);
				image.dy = Math.random() * range * Math.sin(angle);
			}

			// Update position smoothly
			image.x += image.dx * 0.05;
			image.y += image.dy * 0.05;

			// Ensure images wrap around smoothly or stay within the canvas
			switch (mode) {
				case 'bounce':
					if (image.x < image.radius || image.x > canvas.width - image.radius) image.dx *= -1;
					if (image.y < image.radius || image.y > canvas.height - image.radius) image.dy *= -1;
					break;
				case 'wrap':
					if (image.x < -image.radius) image.x = canvas.width + image.radius;
					else if (image.x > canvas.width + image.radius) image.x = -image.radius;
					if (image.y < -image.radius) image.y = canvas.height + image.radius;
					else if (image.y > canvas.height + image.radius) image.y = -image.radius;
					break;
				case 'gravity':
					// Gravity pulls towards the bottom of the canvas
					image.dy += 0.5; // Incremental pull towards bottom
					if (image.y > canvas.height - image.radius) {
						image.dy *= -0.5; // Bounce back with less energy
					}
					break;
			}

			// Save the current context state before clipping
			const visibleRadius = image.radius * ((100 - borderRadius) / 100);

			// Save the current context state before drawing
			ctx.save();

			// Define the path for a rounded rectangle
			roundedRect(
				ctx,
				image.x - image.radius,
				image.y - image.radius,
				image.radius * 2,
				image.radius * 2,
				borderRadius
			);

			// Clip to the path
			ctx.clip();

			// Draw the image within the clipped region
			ctx.drawImage(
				image.element,
				image.x - image.radius,
				image.y - image.radius,
				image.radius * 2,
				image.radius * 2
			);

			// Restore the context to remove the clipping path for the next image
			ctx.restore();

			// Apply a slight friction to slow down over time
			image.dx *= 0.99;
			image.dy *= 0.99;
		});
		requestAnimationFrame(startAnimation);
	}

	// Function to draw a rounded rectangle
	function roundedRect(ctx, x, y, width, height, radius) {
		ctx.beginPath();
		ctx.moveTo(x + radius, y);
		ctx.lineTo(x + width - radius, y);
		ctx.quadraticCurveTo(x + width, y, x + width, y + radius);
		ctx.lineTo(x + width, y + height - radius);
		ctx.quadraticCurveTo(x + width, y + height, x + width - radius, y + height);
		ctx.lineTo(x + radius, y + height);
		ctx.quadraticCurveTo(x, y + height, x, y + height - radius);
		ctx.lineTo(x, y + radius);
		ctx.quadraticCurveTo(x, y, x + radius, y);
		ctx.closePath();
	}
</script>

<div use:sizeCanvas class="canvas-wrapper">
	<canvas bind:this={canvas} width={size.width} height={size.height}></canvas>
</div>

<style>
	.canvas-wrapper {
		display: flex;
		justify-content: center;
		align-items: center;
		height: 100%;
		width: 100%;
	}

	canvas {
		height: 100%;
		width: 100%;
		aspect-ratio: 1 / 1;
	}
</style>
