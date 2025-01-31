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
		isRunning.value = false
		clearInterval(intervalId.value ?? 0)
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

	watch(formattedTime, () => {
		document.title = `✨ ${formattedTime.value} | Pomodoro timer by Célina Ngeth`
	})
</script>

<template>
	<div :class="currentTimer" class="container">
		<div>
			<button
				v-for="(label, timerType) in TIMERS_LABEL"
				:key="timerType"
				type="button"
				@click="currentTimer = timerType"
			>
				{{ label }}
			</button>
		</div>

		<h1>{{ TIMERS_LABEL[currentTimer] }}</h1>

		<div>
			<p class="timer">{{ formattedTime }}</p>

			<button type="button" @click="startPauseTimer()">
				{{ isRunning ? 'Pause' : 'Start' }}
			</button>
			<button type="button" @click="changeTimer()">Next</button>
		</div>
	</div>
</template>

<style lang="css">
	.container {
		height: 100vh;
		width: 100vw;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		transition: background-color 0.5s ease-out;
	}
	.timer {
		font-size: 5rem;
		margin: 0 0.5rem;
	}
	.focus {
		color: #9887b3 !important;
		background-color: #dbcdf0 !important;
	}
	.break {
		color: #b89983 !important;
		background-color: #f7d9c4 !important;
	}
	.longBreak {
		color: #6e8191 !important;
		background-color: #c6def1 !important;
	}
</style>
