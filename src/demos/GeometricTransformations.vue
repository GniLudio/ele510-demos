<template>
	<v-container>
		<v-row justify="center" align="center">
			<v-col cols="3">
				<v-tooltip text="Available Variables: x, y, width, height" location="top">
					<template v-slot:activator="{ props }">
						<v-text-field label="x`" v-bind="props" hide-details v-model="equationX" />
					</template>
				</v-tooltip>
				<v-tooltip text="Available Variables: x, y, width, height" location="bottom">
					<template v-slot:activator="{ props }">
						<v-text-field label="y`" v-bind="props" hide-details v-model="equationY" />
					</template>
				</v-tooltip>
			</v-col>

			<v-col cols="auto" class="d-flex justify-center">
				<v-switch v-model="applyTransformation" color="primary" label="Apply Transformation" />
			</v-col>
		</v-row>
		<canvas class="mx-auto d-block mt-5" ref="canvas"
			style="object-fit: scale-down; min-height: 50vh; max-width: 90%;"></canvas>
	</v-container>
</template>
<script setup lang="ts">
	import bookCover from "@/assets/BookCover.jpg";
	import { parse } from "mathjs";
	import { onMounted, ref, watch } from "vue";

	const canvas = ref<HTMLCanvasElement | null>();

	const applyTransformation = ref(false);

	const equationX = ref("height-1-y");
	const equationY = ref("x");
	const imagePath = ref(bookCover);

	watch([equationX, equationY], () => {
		applyTransformation.value = false;
	})

	watch([applyTransformation, imagePath], updateImage, { flush: 'post' });

	onMounted(() => {
		updateImage();
	});

	function updateImage(): void {
		if (!canvas.value) return;

		const context = canvas.value.getContext("2d");
		if (!context) return;

		context.clearRect(0, 0, canvas.value.width, canvas.value.height);

		const image = new Image();
		image.src = imagePath.value;

		if (!applyTransformation.value) {
			if (!image.loading) {
				drawImage();
			} else {
				image.onload = drawImage;
			}

			function drawImage(): void {
				if (!canvas.value || !context) return;
				canvas.value.width = image.width;
				canvas.value.height = image.height;
				context.drawImage(image, 0, 0);
			}
		} else {
			// get image data
			const offscreenCanvas = new OffscreenCanvas(image.width, image.height);
			offscreenCanvas.width = image.width;
			offscreenCanvas.height = image.height;
			const offscreenContext = offscreenCanvas.getContext("2d");
			if (!offscreenContext) return;
			offscreenContext.drawImage(image, 0, 0);
			const imageData = offscreenContext.getImageData(0, 0, image.width, image.height);

			// calculate transformed coordinates
			const transformedCoordinates: [x: number, y: number][][] = [];
			let [transformedWidth, transformedHeight] = [0, 0];
			const equationXParsed = parse(equationX.value).compile();
			const equationYParsed = parse(equationY.value).compile();

			try {
				const scope = { x: 0, y: 0, width: image.width, height: image.height };
				for (let y = 0; y < image.height; y++) {
					scope.y = y;
					transformedCoordinates.push([]);
					for (let x = 0; x < image.width; x++) {
						scope.x = x;
						const transformedX: number = equationXParsed.evaluate(scope);
						const transformedY: number = equationYParsed.evaluate(scope);
						transformedWidth = Math.max(transformedX, transformedWidth);
						transformedHeight = Math.max(transformedY, transformedHeight);
						transformedCoordinates[y].push([transformedX, transformedY]);
					}
				}
			} catch (Exception) {
				context.fillStyle = "red";
				context.fillRect(0, 0, transformedWidth, transformedHeight);
				return;
			}

			// draw
			canvas.value.width = transformedWidth;
			canvas.value.height = transformedHeight;

			context.fillStyle = "black";
			context.fillRect(0, 0, transformedWidth, transformedHeight);

			for (let y = 0; y < image.height; y++) {
				for (let x = 0; x < image.width; x++) {

					const transformedX = transformedCoordinates[y][x][0];
					const transformedY = transformedCoordinates[y][x][1];
					const index = (y * image.width + x) * 4;
					const red = imageData.data[index + 0];
					const green = imageData.data[index + 1];
					const blue = imageData.data[index + 2];
					const alpha = imageData.data[index + 3];

					context.fillStyle = `rgba(${red}, ${green}, ${blue}, ${alpha / 255})`;
					context.fillRect(transformedX, transformedY, 1, 1);
				}
			}

		}
	}




</script>