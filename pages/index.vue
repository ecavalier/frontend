<template>
  <div class="">
    <div class="card text-light mt-3 max-w-3xl mx-auto">
      <p class="max-w-md mx-auto pb-3 text-center">
        {{ $t('processes.intro') }}
      </p>
      <div class="w-auto max-w-xs mx-auto">
        <n-link class="button cta bg-tertiary mx-auto" to="/category">
          {{ $t('processes.add_product') }}
        </n-link>
      </div>
    </div>
    <div class="mensory-grid">
      <div v-for="entry in news" :key="entry.id" class="card mensory-item">
        <img
          v-if="entry.heroImage"
          :src="`https:${entry.heroImage.fields.file.url}`"
          alt=""
        />
        <h2 v-if="entry.title" class="mt-2 leading-snug">{{ entry.title }}</h2>
        <client-only>
          <timeago
            v-if="entry.createdAt"
            :datetime="entry.createdAt"
            :locale="$store.state.locale"
            class="text-light text-sm"
        /></client-only>
        <div v-if="entry.body" class="mt-2" v-html="entry.body" />
      </div>
    </div>
    <!-- {{ shop }} -->
  </div>
</template>
<script>
import { mapGetters } from 'vuex'
import { createClient } from '~/plugins/modules/contentful.js'
const client = createClient()

export default {
  middleware: 'authenticated',
  async asyncData({ params, store }) {
    let contentfulLocaleCode
    switch (store.state.locale) {
      case 'de':
        contentfulLocaleCode = 'de-DE'
        break
      case 'es':
        contentfulLocaleCode = 'es-ES'
        break
      case 'tr':
        contentfulLocaleCode = 'tr-TR'
        break
      case 'it':
        contentfulLocaleCode = 'it-IT'
        break
      case 'nl':
        contentfulLocaleCode = 'nl-NL'
        break
      default:
        contentfulLocaleCode = 'en'
    }

    try {
      const { items } = await client.getEntries({
        content_type: 'dealerNews',
        locale: contentfulLocaleCode,
        order: '-sys.createdAt',
      })
      const news = items.map((i) => i.fields)
      return { news }
    } catch (e) {
      console.log(e)
      return { news: [] }
    }
  },
  computed: {
    ...mapGetters({
      shop: 'shop/shop',
    }),
  },
}
</script>
<style lang="scss" scoped>
.hero {
  box-shadow: inset 0 0 0 100vw rgba(78, 33, 90, 0.3);
}
</style>
