<template>
  <div class="search">
    <a class="is-icon" role="button" aria-label="Launch search" @click="expand">
      <Sprite symbol="icon-search" class="icon-rg" />
    </a>
    <transition name="dissolve" mode="out-in">
      <div v-if="expanded" class="search-container">
        <div class="search-box" :class="{ 'no-border-radius': query.length > 0 && searchResultsVisible}">
          <input type="text" class="search-input" placeholder="Search blog posts ..." v-model="query" @input="softReset" @keyup="performSearch" @keyup.esc="searchResultsVisible = false" @keydown.up.prevent="highlightPrev" @keydown.down.prevent="highlightNext" @keyup.enter="performSearch" @blur="searchResultsVisible = false" @focus="searchResultsVisible = true" ref="search" aria-label="Search blog posts">
          <a role="button" aria-label="Reset search" @click="reset" class="search-reset is-icon">
            <Sprite symbol="icon-reset" class="icon-rg" />
          </a>
        </div>
        <transition name="dissolve" mode="out-in">
          <div v-if="query.length > 0 && searchResultsVisible" class="search-results">
            <div class="results-box" ref="results">
              <section class="result-label">
                {{ results.length > 0 ? results.length === 1 ? `${results.length} result` : `${results.length} results` : `🙁 No results found for "${this.query}"` }}
              </section>
              <a v-for="(post, index) in results" :key="index" :href="post.item.path" @click="reset" class="result-item">
                <span class="result-title">{{ post.item.title }}</span>
              </a>
            </div>
          </div>
        </transition>
      </div>
    </transition>
  </div>
</template>

<script>
import axios from 'axios'
import Sprite from './Sprite'

import * as appConfig from '@/app.config'
const { search, paths } = appConfig

export default {
  components: {
    Sprite
  },
  created() {
    axios(`/${paths.search.name}`).then(response => {
      this.posts = response.data
    }).catch(error => {
      console.log(error)
    })
  },
  data() {
    return {
      query: '',
      results: [],
      posts: [],
      highlightedIndex: 0,
      searchResultsVisible: false,
      options: search,
      expanded: false
    }
  },
  methods: {
    reset() {
      this.query = ''
      this.highlightedIndex = 0
      this.expanded = !this.expanded
    },
    softReset() {
      this.highlightedIndex = 0
      this.searchResultsVisible = true
    },
    performSearch() {
      this.$search(this.query, this.posts, this.options).then(results => {
        this.results = results
      })
    },
    highlightPrev() {
      if (this.highlightedIndex > 0) {
        this.highlightedIndex = this.highlightedIndex - 1
        this.scrollIntoView()
      }
    },
    highlightNext() {
      if (this.highlightedIndex < this.results.length - 1) {
        this.highlightedIndex = this.highlightedIndex + 1
        this.scrollIntoView()
      }
    },
    scrollIntoView() {
      this.$refs.results.children[this.highlightedIndex].scrollIntoView({ block: 'nearest' })
    },
    expand(e) {
      this.expanded = !this.expanded

      if (this.expanded) {
        this.$nextTick(() => {
          this.$refs.search.focus()
        })
      }
    }
  }
}
</script>
