<template>
  <Layout>
    <template #hero>
      <Hero>
        <h1 class="title">
          <span class="gap-ch-sm hidden-sm">&mdash;</span>Reflections on <br class="hidden-sm" />design and development <br class="hidden-sm" />by <g-link to="/profile/naiyer/">Naiyer Asif</g-link>
        </h1>
      </Hero>
      <div class="post-main-section" @click="$router.push(mostRecent.node.path)">
        <div class="post-main-section-header">
          <span class="posts-pill">
            <strong>Most recent</strong>
            <span class="separator"></span>
            <time v-html="mostRecent.node.date" />
          </span>
        </div>
        <div class="post-main-section-title">
          <g-link :to="mostRecent.node.path">{{ mostRecent.node.title }}</g-link>
        </div>
        <div class="post-main-section-excerpt" v-html="excerpt(mostRecent.node.excerpt)" />
      </div>
    </template>
    <main class="posts">
      <div class="post-section">
        <div class="post-section-header">
          <strong class="posts-pill">Popular this month</strong>
        </div>
        <div class="post-section-item" v-for="popular in $page.popularPosts.edges" :key="popular.id" @click="$router.push(popular.node.path)">
          <div class="post-section-item-title">
            <span class="separator hidden-sm"></span>
            <g-link :to="popular.node.path">{{ popular.node.title }}</g-link>
          </div>
        </div>
      </div>

      <div class="post-section">
        <div class="post-section-header">
          <strong class="posts-pill">Other recent posts</strong>
        </div>
        <div class="post-section-item" v-for="latest in recent" :key="latest.id" @click="$router.push(latest.node.path)">
          <div class="post-section-item-title">
            <time v-html="latest.node.date" />
            <g-link :to="latest.node.path">{{ latest.node.title }}</g-link>
          </div>
        </div>
        <div class="post-section-footer">
          <g-link to="/blog/2/">Browse more &rarr;</g-link>
        </div>
      </div>
    </main>
  </Layout>
</template>

<page-query>
query {
  popularPosts: allPopularBlog(sortBy: "views", order: DESC) {
    edges {
      node {
        id
        title
        path
      }
    }
  }
  latestPosts: allBlog(sortBy: "date", order: DESC, limit: 10) {
    edges {
      node {
        id
        title
        path
        date (format: "MMM D, Y")
        excerpt
      }
    }
  }
}
</page-query>

<script>
import Hero from '~/components/partials/Hero'

export default {
  metaInfo: {
    title: 'Home'
  },
  components: {
    Hero
  },
  computed: {
    mostRecent() {
      return this.$page.latestPosts.edges[0]
    },
    recent() {
      return this.$page.latestPosts.edges.slice(1)
    }
  },
  methods: {
    excerpt(text) {
      return text.endsWith('.') ? text + '..' : text + '...'
    }
  }
}
</script>
