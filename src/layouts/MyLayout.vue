<template>
  <q-layout>
  <q-layout-header :reveal="reveal">
    <q-toolbar color="white">
      <q-toolbar-title class="text-black" :inverted="$q.theme === 'ios'">
        DLIKE
      </q-toolbar-title>
    </q-toolbar>
  </q-layout-header>
  <q-page-container>
    <q-page class="row justify-center">
      <q-list no-border>
        <q-item v-for='item in items' :key='item.title' sparse>
          <q-card inline class="q-ma-sm no-wrap" style="width: auto">
            <q-item>
              <q-item-side :avatar="item.profilePic" />
              <q-item-main>
                <q-item-tile label>{{item.author}}</q-item-tile>
                <q-item-tile sublabel>Subhead</q-item-tile>
              </q-item-main>
            </q-item>
            <q-card-media align="center">
              <img :src="item.image" style="width: 95vw; height: 70vw; object-fit: cover;" class="cover" />
            </q-card-media>
            <q-card-title class="relative-position">
              <div>{{item.title}}</div>
            </q-card-title>
            <q-card-separator />
            <q-card-actions align="between">
              <div class="left">
                <span class="justify-center text-grey">0 </span>
                <span class="justify-center text-grey"> likes</span>
                <span class="justify-center text-grey"> |</span>
                <q-btn flat color="black" icon="comment" />
                <span class="justify-center text-grey">0</span>
              </div>
              <div class="right">
                <span class="justify-center text-grey">$ </span>
                <span class="justify-center text-grey">0.00</span>
                <span class="justify-center text-grey"> |</span>
                <q-btn flat color="black" icon="favorite" />
              </div>
            </q-card-actions>
          </q-card>
          <q-item-separator />
        </q-item>
      </q-list>
    </q-page>
  </q-page-container>
</q-layout>
</template>

<script>
export default {
  data () {
    return {
      items: [],
      reveal: true
    }
  },
  methods: {
    getAcc: async function (user) {
      var account
      var profilePicUrl
      await this.$steemClient.database.getAccounts([user]).then(function (defs) {
        account = defs
        let json = JSON.parse(account[0].json_metadata)
        profilePicUrl = json.profile.profile_image
        console.log(profilePicUrl + '-----------------------------------' + user)
      })
      console.log(profilePicUrl)
      return profilePicUrl
    },
    func: async function () {
      let ip = 0
      while (ip < 7) {
        await this.$steemClient.database.getDiscussions('trending', {
          tag: 'dlike',
          limit: 11
        }).then((discussion) => {
          for (let i = 0; i < 11; i++) {
            // Get user info
            let jsonM = JSON.parse(discussion[i].json_metadata)
            if (jsonM.community === 'dlike') {
              // User account retreival profile_pic_url ppu
              this.getAcc(discussion[i].author).then((data) => {
                if (jsonM.image[0] !== 'h') {
                  this.items.push({
                    title: discussion[i].title.toString(),
                    image: jsonM.image[0],
                    author: discussion[i].author,
                    profilePic: data
                  })
                } else if (jsonM.image[0] === 'h') {
                  this.items.push({
                    title: discussion[i].title.toString(),
                    image: jsonM.image,
                    author: discussion[i].author,
                    profilePic: data
                  })
                }
                ip++
              })
            }
          }
        })
      }
    }
  },
  mounted () {
    this.func().catch(console.error)
    // just so you can do it this way too
    // this.mainy().catch(console.error)
  }
}
</script>

<style>
</style>
