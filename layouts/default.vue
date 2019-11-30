<template lang="pug">
  v-app
    v-navigation-drawer(v-model="drawer" :mini-variant="miniVariant" :clipped="clipped" fixed app)
      v-list
        v-list-item(v-for="(item, i) in items" :key="i" :to="item.to" router exact)
          v-list-item-action
            v-icon {{ item.icon }}
          v-list-item-content
            v-list-item-title(v-text="item.title")
    v-app-bar(:clipped-left="clipped" fixed app)
      v-app-bar-nav-icon(@click.stop="drawer = !drawer")

      v-toolbar-title(v-text="title")
      v-spacer

    v-content
      v-container(pa-5)
        nuxt

    v-footer(:fixed="fixed" app)
      span &copy; Lab 27th
</template>

<script>
export default {
  data () {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: 'mdi-microphone',
          title: '音声認識',
          to: '/'
        },
        {
          icon: 'mdi-voice',
          title: '読み上げ・多国語翻訳',
          to: '/'
        }
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Entwurf (0.4 in beta)'
    }
  }
}
</script>
