<script setup lang="ts">
	import { computed, ref, watch } from 'vue'

	const TIMERS: Record<string, number> = {
		focus: 0.1 * 60,
		break: 0.05 * 60,
		longBreak: 0.07 * 60,
	}

	const isRunning = ref(false)
	let currentTimer = ref('focus')
	const timer = ref(TIMERS[currentTimer.value])

	const formattedTime = computed(() => {
		const minutes = Math.floor(timer.value / 60)
		const seconds = timer.value % 60
		return `${minutes}:${seconds < 10 ? '0' + seconds : seconds}`
	})

	watch(timer, () => {
		formattedTime.value
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
					clearInterval(intervalId.value ?? 0)
					isRunning.value = false

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

					timer.value = TIMERS[currentTimer.value]
				}
			}, 1000)
		}
	}

	const intervalId = ref<number | null>(null)
</script>

<template>
	<h1>{{ currentTimer }}</h1>

	<button type="button" @click="timer = TIMERS['focus']">Focus</button>
	<button type="button" @click="timer = TIMERS['break']">Break</button>
	<button type="button" @click="timer = TIMERS['longBreak']">Long break</button>

	<div class="card">
		<p>{{ formattedTime }}</p>

		<button type="button" @click="startPauseTimer()">
			{{ isRunning ? 'Pause' : 'Start' }}
		</button>
	</div>
</template>
