<template>
	<v-app>
		<v-main class="d-flex flex-column">
			<v-tabs v-model="tab" align-tabs="center" color="primary">
				<v-tab :value="1">Image as a Function</v-tab>
				<v-tab :value="2">Geometric Transformations</v-tab>
			</v-tabs>
			<v-container fluid>
				<v-tabs-window v-model="tab">
					<v-tabs-window-item :value="1">
						<ImageAsAFunction />
					</v-tabs-window-item>
				</v-tabs-window>
			</v-container>

		</v-main>
	</v-app>
</template>

<script setup lang="ts">
	import { ref } from 'vue'
	import ImageAsAFunction from './demos/ImageAsAFunction.vue';

	const tab = ref(1);
</script>
