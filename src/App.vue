<template>
	<v-app>
		<v-main class="d-flex flex-column">
			<v-tabs v-model="tab" align-tabs="center">
				<v-tab value="1">Image as a Function</v-tab>
			</v-tabs>
			<v-container class="fill-height">
				<v-card class="fill-height flex-grow-1 d-flex">
					<v-tabs-window class="w-100 fill-height" v-model="tab">
						<v-tabs-window-item value="1" class="fill-height">
							<ImageAsAFunction></ImageAsAFunction>
						</v-tabs-window-item>
					</v-tabs-window>
				</v-card>

			</v-container>


		</v-main>
	</v-app>
</template>

<script setup lang="ts">
	import { onMounted, ref } from 'vue'
	import ImageAsAFunction from './demos/ImageAsAFunction.vue';

	const tab = ref(1);
	/*
	const canvas = ref<HTMLCanvasElement | null>(null);
	onMounted(() => {
		const resizeCanvas = () => {
			canvas.value!.width = window.innerWidth;
			canvas.value!.height = window.innerHeight;
		}
		resizeCanvas()
		window.addEventListener('resize', resizeCanvas)
	});
	*/
</script>
