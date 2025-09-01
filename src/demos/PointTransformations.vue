<template>
	<v-container>
		<v-row justify="center" align="center">
			<v-col cols="3">
				<v-tooltip text="Applied to each channel - Available Variables: I" location="top">
					<template v-slot:activator="{ props }">
						<v-text-field label="I`" v-bind="props" hide-details v-model="equation" />
					</template>
				</v-tooltip>
			</v-col>
			<v-col cols="1" class="ma-0 pa-0 ga-0">
				<v-sparkline v-model="graph" height="100" padding="0" :min="0 - 4" :max="255 + 4" class="border" />
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
	import { computed, onMounted, ref, watch } from "vue";

	const canvas = ref<HTMLCanvasElement | null>();
	const applyTransformation = ref(false);
	const equation = ref("I > 128 ? 0 : 255");
	const graph = computed(() => {
		return Array.from({ length: 255 }, (_, i) => {
			if (!evaluate.value) return undefined;
			try {
				return Math.max(0, Math.min(255, evaluate.value({ I: i })));
			} catch {
				return undefined;
			}
		})
	});

	const evaluate = computed(() => {
		try {
			return parse(equation.value).compile().evaluate;
		} catch {
			return undefined;
		}
	});

	watch([applyTransformation, evaluate], updateImage, { flush: 'post' });

	onMounted(() => updateImage());

	async function updateImage(): Promise<void> {
		if (!canvas.value) return;

		const context = canvas.value.getContext("2d");
		if (!context) return;

		context.clearRect(0, 0, canvas.value.width, canvas.value.height);

		const image = new Image();
		image.src = bookCover;

		if (!applyTransformation.value) {
			await drawImage();
		} else if (evaluate.value == undefined) {
			context.fillStyle = "grey";
			context.fillRect(0, 0, canvas.value.width, canvas.value.width);
		} else {
			// get image data
			await drawImage();
			const imageData = context.getImageData(0, 0, image.width, image.height);

			// transform colors
			try {
				for (let y = 0; y < image.height; y++) {
					for (let x = 0; x < image.width; x++) {
						const index = (y * image.width + x) * 4;
						imageData.data[index + 0] = evaluate.value({ I: imageData.data[index + 0] });
						imageData.data[index + 1] = evaluate.value({ I: imageData.data[index + 1] });
						imageData.data[index + 2] = evaluate.value({ I: imageData.data[index + 2] });
					}
				}

				// draw
				context.putImageData(imageData, 0, 0);

			} catch {
				context.fillStyle = "grey";
				context.fillRect(0, 0, canvas.value.width, canvas.value.width);
			}
		}

		async function drawImage(): Promise<void> {
			if (!canvas.value || !context || !image) return;
			if (image.loading) {
				await new Promise<void>((resolve) => {
					image.onload = () => resolve();
				});
			}
			canvas.value.width = image.width;
			canvas.value.height = image.height;
			context.drawImage(image, 0, 0);
		}
	}
</script>