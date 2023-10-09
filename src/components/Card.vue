<template>
  <main class="flex flex-col items-center justify-center w-full
              pt-16 pb-28 max-[682px]:pt-0">
    <!-- Choosed Filters -->
    <Transition enter-from-class="opacity-0" enter-leave-class="opacity-400"
      enter-active-class="transition-opacity duration-400" leave-from-class="opacity-400" leave-to-class="opacity-0"
      leave-active-class="transition-opacity duration-400" mode="out-in">
      <div v-show="selectedFilters.length !== 0" class="absolute top-20 flex flex-row items-center justify-between 
          w-[85vw] h-20 py-4 px-6 bg-white shadow-xl rounded 
          max-[682px]:h-auto max-[682px]:relative max-[682px]:-top-14 max-[682px]:mb-4
          max-[682px]:gap-2">
        <div class="inline-flex items-start justify-center gap-4 
            max-[682px]:flex-wrap max-[682px]:justify-start">
          <a v-for="choice in selectedFilters" href="#" @click.prevent="toggleFilter(choice)" :aria-label="choice"
            class="relative inline-flex items-center justify-center pl-2 bg-[#eef6f7] rounded font-bold text-[#5ba4a4]">
            {{ choice }}
            <span class="ml-2 rounded-tr rounded-br inline-flex items-center justify-center 
                bg-[#5ba4a4] h-8 w-7 hover:bg-[#2c3a3a]">
              <img src="./../assets/icon-remove.svg" alt="Remove Filter Button">
            </span>
          </a>
        </div>

        <a href="#" @click.prevent="selectedFilters = []" aria-label="Clear Filter List"
          class="font-semibold hover:text-[#5ba4a4] hover:underline">Clear</a>
      </div>
    </Transition>
    <div class="flex flex-col items-center justify-center gap-6 
        max-[682px]:gap-20">
      <div v-for="(job, index) in filteredJobs" :key="job.id"
        v-bind:class="[job.class, { 'max-[682px]:mt-20': (index === 0 && selectedFilters < 1) }]" class="flex flex-row items-center justify-between 
              w-[85vw] h-32 py-5 px-6 bg-white shadow-xl rounded
              max-[682px]:flex-col max-[682px]:h-auto max-[682px]:gap-0"
        :class="job.featured ? 'border-l-4 border-[#5ba4a4]' : ''">
        <div class="flex flex-row items-center justify-center gap-2 
        max-[682px]:flex-col max-[682px]:items-start max-[682px]:w-full max-[682px]:pt-9">
          <img class="w-20 max-[682px]:w-14 max-[682px]:absolute max-[682px]:-translate-y-[6.5rem]"
            :src="getImageUrl(job.logo)" :alt="job.logo">

          <!-- Job Info -->
          <div class="flex flex-col items-start justify-stretch w-full h-full 
          ml-6 gap-1 max-[682px]:ml-1 max-[682px]:border-b max-[682px]:pb-4">
            <div class="inline-flex items-center justify-center gap-4">
              <p class="text-[#5ba4a4] font-bold">
                {{ job.company }}
              </p>

              <div class="inline-flex items-center justify-center gap-2">
                <p v-show="job.new" class="bg-[#5ba4a4] pb-0 text-[12px] px-2 py-[2px] rounded-3xl 
                    text-white uppercase">
                  New!
                </p>

                <p v-show="job.featured" class="bg-[#2c3a3a] text-[12px] pb-0 text-sm px-2 py-[2px] rounded-3xl 
                    text-white uppercase">
                  Featured
                </p>
              </div>

            </div>

            <a href="#" class="font-semibold text-lg hover:text-[#5ba4a4]" :aria-label="job.position">
              {{ job.position }}
            </a>

            <div class="inline-flex items-start justify-center gap-2">
              <p class="text-[#7b8e8e] font-medium">
                {{ job.postedAt }}
              </p>
              <span class="text-xs py-1 text-gray-400">•</span>
              <p class="text-[#7b8e8e] font-medium">
                {{ job.contract }}
              </p>
              <span class=" text-xs py-1 text-gray-400">•</span>
              <p class="text-[#7b8e8e] font-medium">
                {{ job.location }}
              </p>
            </div>

          </div>

        </div>

        <!-- Filter Selections -->
        <div class="inline-flex items-end justify-center gap-2 
          max-[885px]:flex-wrap max-[885px]:w-[40%] max-[885px]:justify-end
          max-[682px]:justify-start max-[682px]:w-full max-[682px]:pt-4">
          <a href="#" @click.prevent="toggleFilter(job.role)" :aria-label="job.role" class="bg-[#eef6f7] px-2 py-1 rounded font-bold text-[#5ba4a4] 
        hover:bg-[#5ba4a4] hover:text-white">
            {{ job.role }}
          </a>
          <a href="#" @click.prevent="toggleFilter(job.level)" :aria-label="job.level" class="bg-[#eef6f7] px-2 py-1 rounded font-bold text-[#5ba4a4] 
        hover:bg-[#5ba4a4] hover:text-white">
            {{ job.level }}
          </a>
          <a v-for="lang in job.languages" @click.prevent="toggleFilter(lang)" :aria-label="lang" href="#" class="bg-[#eef6f7] px-2 py-1 rounded font-bold text-[#5ba4a4] 
        hover:bg-[#5ba4a4] hover:text-white">
            {{ lang }}
          </a>
          <a v-for="tool in job.tools" @click.prevent="toggleFilter(tool)" :aria-label="tool" href="#" class="bg-[#eef6f7] px-2 py-1 rounded font-bold text-[#5ba4a4] 
        hover:bg-[#5ba4a4] hover:text-white">
            {{ tool }}
          </a>
        </div>
      </div>
    </div>

  </main>
</template>

<script>
import json from '../json/data.json'
export default {
  data() {
    return {
      jobs: json,
      selectedFilters: []
    }
  },
  setup() {
    const getImageUrl = (name) => {
      return new URL(`../assets/${name}`, import.meta.url).href
    }
    return { getImageUrl }
  },
  computed: {
    filteredJobs() {
      if (this.selectedFilters.length === 0) {
        return this.jobs;
      }
      return this.jobs.filter(job => {
        return this.selectedFilters.every(filter => {
          return job.role === filter || job.level === filter || job.languages.includes(filter) || job.tools.includes(filter);
        });
      });
    }
  },
  methods: {
    toggleFilter(filter) {
      const index = this.selectedFilters.indexOf(filter);
      if (index === -1) {
        this.selectedFilters.push(filter);
      } else {
        this.selectedFilters.splice(index, 1);
      }
    }
  },

}
</script>