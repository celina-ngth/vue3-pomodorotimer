<script setup lang="ts">
	import { computed, reactive, ref, watch } from 'vue'
	import { TIMERS, TIMERS_LABEL } from './model'

	const isRunning = ref<boolean>(false)
	const currentTimer = ref<string>('focus')
	const intervalId = ref<number | null>(null)
	const timer = reactive({
		value: TIMERS[currentTimer.value],
	})

	const formattedTime = computed(() => {
		const minutes = Math.floor(timer.value / 60)
		const seconds = timer.value % 60
		return `${minutes}:${seconds < 10 ? '0' + seconds : seconds}`
	})

	const startPauseTimer = () => {
		if (isRunning.value) {
			isRunning.value = false
			clearInterval(intervalId.value ?? 0)
		} else {
			isRunning.value = true
			intervalId.value = setInterval(() => {
				timer.value -= 1

				if (timer.value <= 0) {
					isRunning.value = false
					clearInterval(intervalId.value ?? 0)
					changeTimer()
				}
			}, 1000)
		}
	}

	const changeTimer = () => {
		switch (currentTimer.value) {
			case 'focus':
				currentTimer.value = 'break'
				break
			case 'break':
				currentTimer.value = 'focus'
				break
			case 'longBreak':
				currentTimer.value = 'focus'
				break
		}
	}

	watch(currentTimer, () => {
		timer.value = TIMERS[currentTimer.value]
	})
</script>

<template>
	<button
		v-for="(label, timerType) in TIMERS_LABEL"
		:key="timerType"
		type="button"
		@click="currentTimer = timerType"
	>
		{{ label }}
	</button>

	<h1>{{ TIMERS_LABEL[currentTimer] }}</h1>

	<div class="card">
		<p>{{ formattedTime }}</p>

		<button type="button" @click="startPauseTimer()">
			{{ isRunning ? 'Pause' : 'Start' }}
		</button>
		<button type="button" @click="changeTimer()">Next</button>
	</div>
</template>
