<template>
  <div>
    <v-card v-for="article in articles" :key="article.id" class="article">
      <v-card-title>
        <div>
          <router-link :to="{ name: 'article', params: article }" tag="h3" class="headline mb-0">{{ article.title }}</router-link>
          <div>{{ article.body | short }}...</div>
        </div>
      </v-card-title>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn icon :to="{ name: 'editArticle', params: article }" v-if="$can('update', article)">
          <v-icon>edit</v-icon>
        </v-btn>
        <v-btn icon @click="destroy(article)" v-if="$can('delete', article)">
          <v-icon>delete</v-icon>
        </v-btn>
      </v-card-actions>
    </v-card>
  </div>
</template>

<script>
  import { mapActions } from 'vuex'

  export default {
    data() {
      return {
        articles: []
      }
    },
    methods: {
      ...mapActions('articles', {
        getArticles: 'find',
        deleteArticle: 'destroy'
      }),
      ...mapActions('notifications', ['error']),

      destroy(article) {
        this.$confirm(`Are you sure you want to delete "${article.title}"`, 'Delete Article', { color: 'error', yesLabel: 'Delete' })
          .then(canDelete => canDelete ? this.deleteArticle(article) : null)
          .then(isDestroyed => isDestroyed && this.remove(article))
      },

      remove(article) {
        const index = this.articles.indexOf(article)
        this.articles.splice(index, 1)
      }
    },
    created() {
      this.getArticles()
        .then(articles => this.articles = articles)
        .catch(error => this.error(error.message))
    },
    filters: {
      short(value) {
        return value.slice(0, 250)
      }
    }
  }
</script>

<style scoped lang="scss">
  .article {
    margin-bottom: 20px;
  }

  .headline {
    cursor: pointer;
  }
</style>
