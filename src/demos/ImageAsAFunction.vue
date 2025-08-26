<template>
	<v-container align="center" justify="center">
		<v-row align="center">
			<v-col>
				<v-number-input label="Width" v-model="width" :min="1" :max="5" />
			</v-col>
			<v-col>
				<v-number-input label="Height" v-model="height" :min="1" :max="5" />
			</v-col>
			<v-col>
				<v-number-input label="Bits per Pixel" v-model="bitsPerPixel" :min="1" :max="8" />
			</v-col>
		</v-row>
		<v-label class="text-h5">Matrix</v-label>
		<v-table>
			<tbody>
				<tr v-for="(row, y) in matrix" :key="y">
					<td v-for="(cell, x) in row" :key="x">
						<v-number-input v-model="matrix[y][x]" :min="0" :max="maxPixelValue" />
					</td>
				</tr>
			</tbody>
		</v-table>
		<v-label class="text-h5">Image</v-label>
		<v-container>
			<canvas id="canvas" class="w-50 border border-lg"></canvas>
		</v-container>
	</v-container>

</template>
<script setup lang="ts">
	import { computed, ref, watch, type Ref } from 'vue';

	const width = ref(3);
	const height = ref(3);
	const bitsPerPixel = ref(3);
	const matrix: Ref<number[][]> = ref([
		[0, 1, 2],
		[3, 4, 5],
		[6, 7, 8],
	]);

	const maxPixelValue = computed(() => 2 ** bitsPerPixel.value - 1);

	watch([width, height], ([newWidth, newHeight], [oldWidth, oldHeight]) => {
		const newMatrix: number[][] = [];
		const oldMatrix = matrix.value;

		for (let y = 0; y < newHeight; y++) {
			newMatrix.push([]);
			for (let x = 0; x < newWidth; x++) {
				if (x < (oldWidth ?? 0) && y < (oldHeight ?? 0)) {
					newMatrix[y].push(oldMatrix[y][x]);
				} else {
					newMatrix[y].push(Math.floor(Math.random() * maxPixelValue.value));
				}
			}
		}
		matrix.value = newMatrix;
	});

	watch(matrix, (newValue) => {
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
				const value = Math.round((newValue[y][x] / maxPixelValue.value) * 255);
				context.fillStyle = `rgb(${value}, ${value}, ${value})`;
				context.fillRect(x, y, 1.1, 1.1);
			}
		}

		context.restore();
	}, { deep: true });

	watch(maxPixelValue, (newValue) => {
		for (let y = 0; y < matrix.value.length; y++) {
			for (let x = 0; x < matrix.value[y].length; x++) {
				matrix.value[y][x] = Math.min(newValue, matrix.value[y][x]);
			}
		}
		matrix.value = matrix.value.splice(0);
	});

</script>
<style lang="css" scoped>
	:deep(.v-field__input) {
		text-align: center
	}
</style>