<template>
  <div class="search-suggestion">
    <van-cell
      v-for="(text, index) in Suggestions"
      :key="index"
      icon="search"
      @click="$emit('search', text)"
    >
      <div slot="title" v-html="highlight(text)"></div>
    </van-cell>
  </div>
</template>

<script>
import { getSearchSuggestions } from '@/api/search'
import { debounce } from 'lodash'

export default {
  name: 'SearchSuggestion',
  components: {},
  props: {
    searchText: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      Suggestions: []
    }
  },
  computed: {},
  watch: {
    searchText: {
      handler: debounce(function(value) {
        this.loadSearhSuggestions(value)
      }, 300),
      immediate: true
    }
  },
  created() {},
  mounted() {},
  methods: {
    async loadSearhSuggestions(q) {
      try {
        const { data } = await getSearchSuggestions(q)
        // console.log(data)
        this.Suggestions = data.data.options
      } catch (err) {
        this.$toast('数据获取失败，请稍后重试')
      }
    },
    highlight(text) {
      const highlightStr = `<span class="active">${this.searchText}</span>`
      const reg = new RegExp(this.searchText, 'gi')
      return text.replace(reg, highlightStr)
    }
  }
}
</script>
<style lang="less" scoped>
.search-suggestion {
  /deep/.active {
    color: #3296fa;
  }
}
</style>
