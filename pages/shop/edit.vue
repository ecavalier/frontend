<template>
  <div>
    <edit-shop :core-shop="coreShop" />
  </div>
</template>

<script>
import editShop from '~/components/pageElements/shop/EditShop'

export default {
  // name: 'EditShop', TODO breaks when uncommented. why?
  components: {
    editShop,
  },
  middleware: 'authenticated',
  async asyncData({ $axios, store, query, route, redirect }) {
    try {
      const { user } = store.state
      const coreShop = await $axios.$get(`/api/users/${user._id}/shops/active`)
      return { coreShop }
    } catch (e) {
      store.commit('modal/showModal', {
        message: 'information.error_occurred',
        confirmText: 'refresh_now',
        onConfirm: () => {
          if (process.browser) location.reload()
        },
      })
      console.log(e)
      return { coreShop: {} }
    }
  },
}
</script>

<style lang="css" scoped></style>
