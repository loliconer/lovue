<template>
  <div class="v-tag" @click.stop>
    <input class="input" v-model.trim="tag" @focus="isShowCandidates = true" @keydown.enter="addCustom">
    <div class="t-selected tag-list">
      <a class="tag tag-black" v-for="(t, i) of value" @click="remove(t, i)">{{t.name}}</a>
    </div>
    <div class="t-candidates tag-list" v-show="isShowCandidates" v-if="tags.length">
      <a class="tag tag-white" v-for="(t, i) of tags" @click="add(t, i)">{{t.name}}</a>
      <v-icon icon="close" @click.native="isShowCandidates = false"></v-icon>
    </div>
  </div>
</template>
<script>
  import '../less/extension/Tag.less'
  import Icon from '../components/Icon.vue'

  export default {
    name: 'v-tag',
    data() {
      return {
        tag: '',
        isShowCandidates: false
      }
    },
    props: {
      value: {
        type: Array,
        'default': () => []
      },
      tags: Array,
      max: {
        type: Number,
        'default': 5
      }
    },
    components: {
      [Icon.name]: Icon
    },
    methods: {
      addCustom(ev) {
        ev.preventDefault()
        if (this.add({ name: this.tag, custom: true }, this.tags.findIndex(t => t.name === this.tag))) this.tag = ''
      },
      add(tag, index) {
        if (this.value.length >= this.max) {
          this.warn(`最多只能添加${this.max}个标签`)
          return
        }

        if (this.value.findIndex(t => t.name === tag.name) >= 0) {
          this.warn('该标签已存在')
          return
        }

        this.value.push(tag)
        index >= 0 && this.tags.splice(index, 1)
        return true
      },
      remove(tag, index) {
        this.value.splice(index, 1)
        if (tag.custom) return

        if (this.tags.findIndex(t => t.name === tag.name) < 0) this.tags.push(tag)
      }
    },
    mounted() {
      window.addEventListener('click', () => this.isShowCandidates = false)
    }
  }
</script>
