<template>
	<v-container>
		<v-row justify="center">
			<v-col cols="3">
				<template v-if="forwardMapping">
					<v-tooltip text="Available Variables: x, y, width, height" location="top">
						<template v-slot:activator="{ props }">
							<v-text-field :label="'x` = f(x,y)'" v-bind="props" hide-details v-model="forwardX" />
						</template>
					</v-tooltip>
					<v-tooltip text="Available Variables: x, y, width, height" location="bottom">
						<template v-slot:activator="{ props }">
							<v-text-field :label="'y` = f(x,y)'" v-bind="props" hide-details v-model="forwardY" />
						</template>
					</v-tooltip>
				</template>
				<template v-else>
					<v-tooltip text="Available Variables: x´, y´, width, height" location="top">
						<template v-slot:activator="{ props }">
							<v-text-field :label="'x = f(x´,y´)'" v-bind="props" hide-details v-model="inverseX" />
						</template>
					</v-tooltip>
					<v-tooltip text="Available Variables: x´, y´, width, height" location="bottom">
						<template v-slot:activator="{ props }">
							<v-text-field :label="'y = f(x´,y´)'" v-bind="props" hide-details v-model="inverseY" />
						</template>
					</v-tooltip>

				</template>

			</v-col>
			<v-col cols="2" class="d-flex justify-center">
				<v-switch v-model="forwardMapping" color="primary"
					:label="['Inverse Mapping', 'Forward Mapping'][!forwardMapping ? 0 : 1]" />
			</v-col>
		</v-row>
		<v-row justify="center">
			<v-col cols="2" class="d-flex justify-center">
				<v-switch v-model="applyTransformation" color="primary" label="Apply Transformation" />
			</v-col>
		</v-row>
		<v-img :src="image" style="max-height: 60vh; object-fit: contain;" />
	</v-container>
</template>
<script setup lang="ts">
	import bookCover from "@/assets/BookCover.jpg";
	import { ref, watch } from "vue";

	const applyTransformation = ref(false);
	const forwardMapping = ref(true);

	const forwardX = ref("height-1-y");
	const forwardY = ref("x");
	const inverseX = ref("");
	const inverseY = ref("");
	const image = ref(bookCover);

	watch([forwardX, forwardY, inverseX, inverseY], () => {

	});

</script>