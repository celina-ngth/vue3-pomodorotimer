<script setup lang="ts">
	import { computed, reactive, ref, watch } from 'vue'

	const TIMERS_LABEL: Record<string, string> = {
		focus: 'Focus',
		break: 'Break',
		longBreak: 'Long break',
	}

	const TIMERS: Record<string, number> = {
		focus: 0.1 * 60,
		break: 0.05 * 60,
		longBreak: 0.07 * 60,
	}

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
	<h1>{{ TIMERS_LABEL[currentTimer] }}</h1>

	<button type="button" @click="currentTimer = 'focus'">
		{{ TIMERS_LABEL['focus'] }}
	</button>
	<button type="button" @click="currentTimer = 'break'">
		{{ TIMERS_LABEL['break'] }}
	</button>
	<button type="button" @click="currentTimer = 'longBreak'">
		{{ TIMERS_LABEL['longBreak'] }}
	</button>

	<div class="card">
		<p>{{ formattedTime }}</p>

		<button type="button" @click="startPauseTimer()">
			{{ isRunning ? 'Pause' : 'Start' }}
		</button>
		<button type="button" @click="changeTimer()">Next</button>
	</div>
</template>
