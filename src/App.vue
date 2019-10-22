<template>
  <v-app id="inspire">
    <v-navigation-drawer
      v-model="drawer"
      :clipped="$vuetify.breakpoint.lgAndUp"
      app
      :width="300"
      :mini-variant.sync="mini"
    >
      <v-list-item>
        <v-list-item-icon>
            <v-icon>mdi-youtube-tv</v-icon>
          </v-list-item-icon>
        <v-list-item-title>List Channels</v-list-item-title>

        <v-btn
          icon
          @click.stop="mini = !mini"
        >
          <v-icon>mdi-chevron-left</v-icon>
        </v-btn>
      </v-list-item>

      <v-divider></v-divider>
      <v-list dense >
        <v-list-item-group v-model="channel">
          <v-list-item v-for="c in channels" :key="c"
          link @click="playVideo(c.src)"
          >
            <v-list-item-avatar tile>
              <v-img :src="c.img"></v-img>
            </v-list-item-avatar>

            <v-list-item-content>
              <v-list-item-title>{{c.title}}</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </v-list-item-group>
        
      </v-list>
    </v-navigation-drawer>

    <v-app-bar
      :clipped-left="$vuetify.breakpoint.lgAndUp"
      app
      dark
      color="red"
      dense
    >
      <v-toolbar-title
        style="width: 300px"
      >
        <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
        <v-icon class="mx-4">mdi-youtube</v-icon>
        <span class="hidden-sm-and-down">LiveTV App</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn
        icon
        large
      >
        <v-avatar
          size="32px"
          item
        >
          <v-img
            src="https://cdn.vuetifyjs.com/images/logos/logo.svg"
            alt="Vuetify"
          >
          </v-img></v-avatar>
      </v-btn>
    </v-app-bar>
    <v-content>
      <v-container fluid grid-list-lg fill-height>
        <v-row  no-gutters>
          <v-col>
            <video-player ref="videoPlayer"
                          class="vjs-custom-skin"
                          :options="playerOptions"
                          @ready="onPlayerReady($event)">
            </video-player>
          </v-col>
        </v-row>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
  import {videoPlayer} from 'vue-videojs7'
  export default {
    props: {
      source: String,
    },
    components: {
      videoPlayer
    },
    data: () => ({
      drawer: null,
      mini: false,
      channel: -1,
      channels: [
        {
          src: "http://peer3.savitar.tv/Boomerang/myStream/playlist.m3u8?wmsAuthSign=c2VydmVyX3RpbWU9MTAvMjIvMjAxOSA3OjI1OjEzIEFNJmhhc2hfdmFsdWU9ZGR3M2krZ2E2ZEczUys5c1M3R2d3UT09JnZhbGlkbWludXRlcz0zNjAmaWQ9MA==",
          img: "http://ustvgo.tv/wp-content/uploads/2019/08/Boomerang.png",
          title: "Boomerang"
        }
      ],
      playerOptions: {
        autoplay: true,
        controls: true,
        controlBar: {
          timeDivider: false,
          durationDisplay: false
        }
      }
    }),
    computed: {
      player () {
        return this.$refs.videoPlayer.player
      }
    },
    methods: {
      onPlayerReady (player) {
        this.player.play()
      },
      playVideo: function (source) {
        const video = {
          withCredentials: false,
          type: 'application/x-mpegurl',
          src: source
        }
        this.player.reset() // in IE11 (mode IE10) direct usage of src() when <src> is already set, generated errors,
        this.player.src(video)
        this.player.play()
      }
    },
    mounted () {
      // this.playVideo(this.channels[0].src)
    }
  }
</script>
<style scoped>
  .player {
    position: absolute !important;
    width: 50vw;
    height: 100%;
  }
  .vjs-custom-skin {
    height: 90vh !important;
  }

  .vjs-custom-skin /deep/ .video-js {
    height: 100%;
    width: 100%;
  }
</style>
