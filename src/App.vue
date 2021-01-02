<template>
  <div
    id="app"
    :style="{
      'background-image': `linear-gradient(${direction}, ${gradient
        .map(color => `${color}66`)
        .join(',')})${
        options.image ? ', url(https://source.unsplash.com/1920x1080)' : ''
      }`
    }"
  >
    <header>
      <h1 class="brand">Vuedients</h1>
      <div class="links">
        <LogoGithubIcon
          href="https://github.com/"
          :w="links.size"
          :h="links.size"
        />
      </div>
    </header>
    <main>
      <section class="colors">
        <div class="header">
          <h2>Colors</h2>
          <button @click="addGradient()">
            <MdAddIcon w="32" h="32" />
          </button>
        </div>
        <draggable
          v-model="gradient"
          class="inputs"
          @start="drag = true"
          @end="drag = false"
        >
          <div v-for="(color, index) in gradient" :key="index">
            <input v-model="gradient[index]" type="color" />
            <div class="chip" :style="{ background: color }">
              <span>{{ color }}</span>
              <span
                v-if="gradient.length > 2"
                @click="removeGradient(index)"
                class="chip-remove"
              >
                &times;
              </span>
            </div>
          </div>
        </draggable>

        <div class="code">
          <code>
            {{ css }}
          </code>
          <span @click="copyCss()">
            <MdCopyIcon h="32" w="32" />
          </span>
        </div>
      </section>
      <section>
        <h2>Options</h2>
        <div class="options">
          <div>
            <input v-model="options.image" id="imageEnabled" type="checkbox" />
            <label for="imageEnabled">Enable background image</label>
          </div>
        </div>
      </section>
    </main>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import draggable from 'vuedraggable'

import icons from '@/components/icons'
import presets from '@/gradientPresets'

export default Vue.extend({
  name: 'App',
  components: {
    draggable,
    ...icons
  },
  data() {
    return {
      gradient: ['#ff0000', '#00ff00'],
      direction: 'to right',
      links: {
        size: '32'
      },
      options: {
        image: true
      },
      css: '',
      presets
    }
  },
  watch: {
    gradient: {
      immediate: true,
      handler() {
        this.css = this.generateCss()
      }
    }
  },
  methods: {
    addGradient() {
      this.gradient.push(this.randomHexColor())
    },
    removeGradient(index: number) {
      this.gradient.splice(index, 1)
    },
    randomHexColor(): string {
      return `#${(Math.random() * 0xfffff * 1000000).toString(16).slice(0, 6)}`
    },
    generateCss(): string {
      const css = `background: linear-gradient(${
        this.direction
      }, ${this.gradient.join(', ')})`
      return css
    },
    async copyCss() {
      await navigator.clipboard.writeText(this.css)
    }
  }
})
</script>

<style lang="scss">
@import '~vue-ionicons/ionicons.scss';
@import 'scss/main';
</style>
