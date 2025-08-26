<template>
	<v-container align="center" justify="center">
		<v-row align="center">
			<v-col>
				<v-number-input label="Width" v-model="width" :min="0" :max="10" />
			</v-col>
			<v-col>
				<v-number-input label="Height" v-model="height" :min="0" :max="10" />
			</v-col>
			<v-col>
				<v-number-input label="Bits per Pixel/Channel" v-model="bitsPerPixel" :min="1" :max="8" />
			</v-col>
			<v-col cols="auto">
				<v-switch label="Colored" v-model="colored" color="primary" />
			</v-col>
		</v-row>
		<v-label class="text-h5">Matrix</v-label>
		<v-table class="mb-5">
			<tbody>
				<tr v-for="(row, y) in matrix" :key="y">
					<td v-for="(cell, x) in row" :key="x" class="border py-2 overflow-hidden">
						<v-number-input v-if="!colored" v-model="matrix[y][x]" :min="0" :max="maxPixelValue"
							hide-details density="compact" control-variant="hidden" />
						<v-row v-else>
							<v-col v-for="i in [0, 1, 2]" :key="i"
								:class="{ 'pr-0': i == 0, 'px-1': i == 1, 'pl-0': i == 2 }">
								<v-number-input v-model="coloredMatrix[y][x][i]" :min="0" :max="maxPixelValue"
									:label="['Red', 'Green', 'Blue'][i]" v-if="colored" hide-details density="compact"
									control-variant="hidden" />
							</v-col>
						</v-row>

					</td>
				</tr>
			</tbody>
		</v-table>
		<v-label class="text-h5">Image</v-label><br />
		<canvas id="canvas" class="w-50 border border-lg"></canvas>
	</v-container>

</template>
<script setup lang="ts">
	import { computed, ref, watch, type Ref } from 'vue';

	const width = ref(3);
	const height = ref(3);
	const bitsPerPixel = ref(3);
	const colored = ref(false);
	const matrix: Ref<number[][]> = ref([
		[0, 1, 2],
		[3, 4, 5],
		[6, 7, 8],
	]);
	const coloredMatrix: Ref<[number, number, number][][]> = ref([
		[[7, 0, 0], [0, 7, 0], [0, 0, 7]],
		[[7, 7, 0], [7, 7, 0], [7, 0, 7]],
		[[7, 0, 8], [0, 7, 7], [0, 7, 7]],
	]);

	const maxPixelValue = computed(() => 2 ** bitsPerPixel.value - 1);

	watch([width, height], ([newWidth, newHeight], [oldWidth, oldHeight]) => {
		const newMatrix: number[][] = [];
		const newColoredMatrix: [number, number, number][][] = [];

		const oldMatrix = matrix.value;
		const oldColoredMatrix = coloredMatrix.value;

		for (let y = 0; y < newHeight; y++) {
			newMatrix.push([]);
			newColoredMatrix.push([]);
			for (let x = 0; x < newWidth; x++) {
				if (x < (oldWidth ?? 0) && y < (oldHeight ?? 0)) {
					newMatrix[y].push(oldMatrix[y][x]);
					newColoredMatrix[y].push([oldColoredMatrix[y][x][0], oldColoredMatrix[y][x][1], oldColoredMatrix[y][x][2]])
				} else {
					newMatrix[y].push(Math.floor(Math.random() * maxPixelValue.value));
					newColoredMatrix[y].push([Math.floor(Math.random() * maxPixelValue.value), Math.floor(Math.random() * maxPixelValue.value), Math.floor(Math.random() * maxPixelValue.value)])
				}
			}
		}
		matrix.value = newMatrix;
		coloredMatrix.value = newColoredMatrix;
	});

	watch([matrix, coloredMatrix, colored], () => {
		const canvas = document.getElementById("canvas") as HTMLCanvasElement | null;
		if (!canvas) return;

		const context = canvas.getContext("2d");
		if (!context) return;


		context.clearRect(0, 0, canvas.width, canvas.height);
		if (width.value == 0 || height.value == 0) return;

		context.save();
		context.scale(canvas.width / width.value, canvas.height / height.value);

		context.imageSmoothingEnabled = false;
		canvas.style.imageRendering = "pixelated";

		for (let y = 0; y < height.value; y++) {
			for (let x = 0; x < width.value; x++) {
				if (!colored.value) {
					const value = Math.round((matrix.value[y][x] / maxPixelValue.value) * 255);
					context.fillStyle = `rgb(${value}, ${value}, ${value})`;
					context.fillRect(x, y, 1, 1);
				} else {
					const value = coloredMatrix.value[y][x];
					const rValue = Math.round((value[0] / maxPixelValue.value) * 255);
					const gValue = Math.round((value[1] / maxPixelValue.value) * 255);
					const bValue = Math.round((value[2] / maxPixelValue.value) * 255);
					context.fillStyle = `rgb(${rValue}, ${gValue}, ${bValue})`;
					context.fillRect(x, y, 1, 1);
				}

			}
		}

		context.restore();
	}, { deep: true });

	watch(maxPixelValue, () => {
		for (let y = 0; y < matrix.value.length; y++) {
			for (let x = 0; x < matrix.value[y].length; x++) {
				matrix.value[y][x] = Math.min(maxPixelValue.value, matrix.value[y][x]);
				coloredMatrix.value[y][x] = [
					Math.min(maxPixelValue.value, coloredMatrix.value[y][x][0]),
					Math.min(maxPixelValue.value, coloredMatrix.value[y][x][1]),
					Math.min(maxPixelValue.value, coloredMatrix.value[y][x][2]),
				]
			}
		}
		matrix.value = matrix.value.splice(0);
		coloredMatrix.value = coloredMatrix.value.splice(0);
	});

</script>
