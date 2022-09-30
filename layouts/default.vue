<template>
  <v-app>
    <v-navigation-drawer
      v-model="drawer"
      fixed
      app
    >
      <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title v-text="item.title" />
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar color="primary" dark fixed app>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <v-toolbar-title v-text="title" />
      <v-spacer />
      <v-btn icon :href="tweetUrl" target="_blank">
        <v-icon>mdi-twitter</v-icon>
      </v-btn>
      <v-btn icon @click.stop="rightDrawer = !rightDrawer">
        <v-icon>mdi-help-box</v-icon>
      </v-btn>
    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <v-navigation-drawer v-model="rightDrawer" right temporary fixed>
      <v-list>
        <v-list-item>
          <v-list-item-action>
            <v-icon light> mdi-help-box </v-icon>
          </v-list-item-action>
          <v-list-item-title>TODO: ヘルプか何か入れる</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-footer app>
      <span>&copy; {{ new Date().getFullYear() }} IWABUCHI Yu(u)ki butchi</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: 'DefaultLayout',
  data() {
    return {
      drawer: false,
      items: [
        {
          icon: 'mdi-gamepad-variant',
          title: 'プレイ',
          to: '/',
        },
        {
          icon: 'mdi-help',
          title: '遊び方',
          to: '/how-to-play/',
        },
        {
          icon: 'mdi-trophy',
          title: '出会いテム一覧',
        },
      ],
      rightDrawer: false,
      title: '汲斬 #QumiKiri (仮)',
    }
  },
  computed: {
    tweetUrl() {
      let ret

      const siteUrl = "https://qumikiri.butchi.jp/"
      const tweetTxt = "汲斬"
      const hashtags = ["QumiKiri"]

      const tweetUrl = `https://twitter.com/intent/tweet?text=${tweetTxt}&url=${siteUrl}&hashtags=${hashtags}`

      ret = tweetUrl

      return ret
    }
  }
}
</script>
