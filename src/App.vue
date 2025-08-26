<template>
	<v-app>
		<v-main class="d-flex flex-column">
			<v-tabs v-model="tab" align-tabs="center" color="primary">
				<v-tab value="image_as_a_function">Image as a Function</v-tab>
				<v-tab value="geometric_transformations">Geometric Transformations</v-tab>
			</v-tabs>
			<v-container fluid>
				<v-tabs-window v-model="tab">
					<v-tabs-window-item value="image_as_a_function">
						<ImageAsAFunction />
					</v-tabs-window-item>
					<v-tabs-window-item value="geometric_transformations">
						<GeometricTransformations />
					</v-tabs-window-item>
				</v-tabs-window>
			</v-container>

		</v-main>
	</v-app>
</template>

<script setup lang="ts">
	import { ref } from 'vue'
	import ImageAsAFunction from './demos/ImageAsAFunction.vue';
	import GeometricTransformations from './demos/GeometricTransformations.vue';

	const tab = ref("geometric_transformations");
</script>

<style lang="css">
	.v-field__input {
		text-align: center
	}

	.v-field__field {
		justify-content: center;
	}
</style>