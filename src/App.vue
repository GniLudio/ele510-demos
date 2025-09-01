<template>
	<v-app>
		<v-main class="d-flex flex-column">
			<v-tabs v-model="tab" align-tabs="center" color="primary">
				<v-tab value="image_as_a_function">Image as a Function</v-tab>
				<v-tab value="geometric_transformations">Geometric Transformations</v-tab>
				<v-tab value="point_transformations">Point Transformations</v-tab>
			</v-tabs>
			<v-container fluid>
				<v-tabs-window v-model="tab">
					<v-tabs-window-item value="image_as_a_function">
						<ImageAsAFunction />
					</v-tabs-window-item>
					<v-tabs-window-item value="geometric_transformations">
						<GeometricTransformations />
					</v-tabs-window-item>
					<v-tabs-window-item value="point_transformations">
						<PointTransformations />
					</v-tabs-window-item>
				</v-tabs-window>
			</v-container>
			<v-footer class="position-absolute bottom-0 right-0"><a
					href="https://github.com/GniLudio/ele510-demos/">GitHub</a></v-footer>
		</v-main>
	</v-app>
</template>

<script setup lang="ts">
	import { ref } from 'vue'
	import ImageAsAFunction from './demos/ImageAsAFunction.vue';
	import GeometricTransformations from './demos/GeometricTransformations.vue';
	import PointTransformations from './demos/PointTransformations.vue';

	const tab = ref("point_transformations");
</script>

<style lang="css">
	.v-field__input {
		text-align: center
	}

	.v-field__field {
		justify-content: center;
	}
</style>