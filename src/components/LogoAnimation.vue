<script setup>
import {computed, ref} from 'vue';
import {useMouse, useWindowSize} from '@vueuse/core';
import LogoIcon from '@/components/icons/LogoIcon.vue';

const logo = ref();
const {x, y} = useMouse();
const {width, height} = useWindowSize();

const distX = computed(() => Math.abs(x.value - width.value / 2));
const distY = computed(() => Math.abs(y.value - height.value / 2));
const distance = computed(() => Math.sqrt(distX.value * distX.value + distY.value * distY.value));
const size = computed(() => Math.max(500 - distance.value, 150));
const opacity = computed(() => Math.min(Math.max(size.value / 300, 0.7), 1));

const logoGradient = computed(() => {
	const rect = logo.value?.getBoundingClientRect()
	const xPos = x.value - (rect?.left || 0)
	const yPos = y.value - (rect?.top || 0)
	return `radial-gradient(circle at ${xPos}px ${yPos}px, black 30%, transparent 100%)`
});
</script>

<template>
	<div class="wrapper">
		<div class="background"/>
		<div class="cursor-mouse"
			 :style="{
				opacity,
				left: `${x}px`,
				top: `${y}px`,
				width: `${size}px`,
				height: `${size}px`
			 }"
		/>
		<div ref="logo" :style="{maskImage: logoGradient}">
			<LogoIcon/>
		</div>
	</div>
</template>

<style scoped>
.background {
	@apply absolute inset-0 w-full h-full pointer-events-none
	bg-gradient-to-b from-black to-primary-green from-90% blur-3xl;
}

.wrapper {
	@apply w-screen h-screen
	flex items-center justify-center relative overflow-hidden;
}

.cursor-mouse {
	@apply absolute bg-primary-green rounded-full blur-3xl
	-translate-y-1/2 -translate-x-1/2 pointer-events-none;
}
</style>
