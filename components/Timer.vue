<template>
  <div
    class="relative border rounded shadow px-4 py-2 m-4 text-gray-700"
    style="width: 16rem; height: 8rem"
    @click="stop()"
  >
    <div class="flex gap-4 h-full">
      <div class="flex items-center">
        <div
          class="w-20 h-20 border-4 border-gray-200 rounded-full flex justify-center items-center"
        >
          <div class="cursor-pointer">
            <div v-if="sound === null">
              <font-awesome-icon
                v-if="!active"
                :icon="['fas', 'play']"
                @click="start()"
              />
              <font-awesome-icon
                v-else
                :icon="['fas', 'pause']"
                @click="pause()"
              />
            </div>
            <font-awesome-icon
              v-else
              :icon="['fas', 'volume-mute']"
              class="text-red-600"
            />
          </div>
        </div>
      </div>
      <div class="flex gap-4 items-center">
        <div class="flex flex-col gap-3 text-sm">
          <input
            type="text"
            :class="(active ? 'timer-name ' : 'bg-gray-200 ') + 'rounded p-1'"
            style="width: 7.4rem"
            value="Timer"
          />
          <div class="flex gap-1 w-32 h-8">
            <div v-if="!active">
              <form action="#" @submit.prevent="start()">
                <input
                  v-model="hours"
                  type="tel"
                  class="bg-gray-200 rounded p-1 w-8 text-center"
                  placeholder="h"
                  maxlength="2"
                  @blur="handleInput()"
                />
                <span>:</span>
                <input
                  v-model="minutes"
                  type="tel"
                  class="bg-gray-200 rounded p-1 w-8 text-center"
                  placeholder="m"
                  maxlength="2"
                  @blur="handleInput()"
                />
                <span>:</span>
                <input
                  v-model="seconds"
                  type="tel"
                  class="bg-gray-200 rounded p-1 w-8 text-center"
                  placeholder="s"
                  maxlength="2"
                  @blur="handleInput()"
                />
                <input type="submit" class="hidden" />
              </form>
            </div>
            <div v-else class="flex gap-1 px-1 font-bold">
              <span class="text-3xl leading-none"
                >{{ hours ? hours : '00' }}:{{ minutes ? minutes : '00' }}</span
              >
              <span
                class="relative text-lg text-gray-500 leading-none"
                style="bottom: -0.2rem"
                >{{ seconds ? seconds : '00' }}</span
              >
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  data() {
    return {
      active: false as boolean,
      timerInterval: null as any,
      sound: null as HTMLAudioElement | null,
      finished: false as boolean,
      hours: '' as string,
      minutes: '01' as string,
      seconds: '00' as string,
    }
  },
  methods: {
    start() {
      if (
        (isNaN(Number(this.hours)) || Number(this.hours) === 0) &&
        (isNaN(Number(this.minutes)) || Number(this.minutes) === 0) &&
        (isNaN(Number(this.seconds)) || Number(this.seconds) === 0)
      ) {
        return
      }

      this.active = true

      this.timerInterval = setInterval(() => {
        const seconds = Number(this.seconds)
        const minutes = Number(this.minutes)
        const hours = Number(this.hours)

        if (seconds > 0) {
          this.seconds = (seconds - 1).toString()
        } else if (minutes > 0) {
          this.minutes = (minutes - 1).toString()
          this.seconds = '59'
        } else if (hours > 0) {
          this.hours = (hours - 1).toString()
          this.minutes = this.seconds = '59'
        } else {
          clearInterval(this.timerInterval)
          this.sound = new Audio('/sound.ogg')
          this.sound.addEventListener(
            'ended',
            () => {
              if (!this.sound) {
                return
              }

              this.sound.currentTime = 0
              this.sound.play()
            },
            false
          )
          this.sound.play()

          return
        }

        this.handleInput()
      }, 1000)
    },
    pause() {
      this.active = false
      clearInterval(this.timerInterval)
    },
    handleInput() {
      const hours = this.hours

      if (undefined !== hours) {
        if (hours.length === 1) {
          this.hours = '0' + hours
        }
      }

      const minutes = this.minutes

      if (undefined !== minutes) {
        if (Number(minutes) > 59) {
          this.minutes = '59'
        }

        if (minutes.length === 1) {
          this.minutes = '0' + minutes
        }
      }

      const seconds = this.seconds

      if (undefined !== seconds) {
        if (Number(seconds) > 59) {
          this.seconds = '59'
        }

        if (seconds.length === 1) {
          this.seconds = '0' + seconds
        }
      }
    },
    stop() {
      if (this.sound === null) {
        return
      }

      this.sound.pause()
      this.sound = null
      this.active = false
    },
  },
})
</script>

<style scoped>
.timer-name:focus,
.timer-name:hover {
  @apply bg-gray-200;
}
</style>
