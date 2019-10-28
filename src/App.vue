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
        <v-skeleton-loader
          :loading="loading"
          transition="fade-transition"
          height="94"
          type="list-item-avatar"
        >
          <v-list-item-group v-model="channel">
            <v-list-item v-for="c in channels" :key="c"
            link @click="playVideo(c.src)" :title="c.title"
            >
              <v-list-item-avatar tile>
                <v-img :src="c.img"></v-img>
              </v-list-item-avatar>

              <v-list-item-content>
                <v-list-item-title>{{c.title}}</v-list-item-title>
              </v-list-item-content>
            </v-list-item>
          </v-list-item-group>
        </v-skeleton-loader>
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
            src="@/assets/ARMUSIC_LOGO.png"
            alt="LiveTV"
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
  const axios = require('axios');

  export default {
    props: {
      source: String,
    },
    components: {
      videoPlayer
    },
    data: () => ({
      drawer: null,
      loading: true,
      mini: false,
      channel: -1,
      auth: '',
      channels: [
        {
          src: "http://peer3.savitar.tv/AE/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/AE.png",
          title: "A&E"
        },
        {
          src: "http://peer3.savitar.tv/ABC/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2018/10/abc-269x151.jpg",
          title: "ABC"
        },
        {
          src: "http://peer3.savitar.tv/AMC/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/AMC-1.png",
          title: "AMC"
        },
        {
          src: "http://peer3.savitar.tv/Boomerang/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/08/Boomerang.png",
          title: "Boomerang"
        },
        {
          src: "http://peer3.savitar.tv/CN/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/cartoon-network.jpg",
          title: "Cartoon Network"
        },
        {
          src: "http://peer3.savitar.tv/ESPN/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/espn-269x151.png",
          title: "ESPN"
        },
        {
          src: "http://peer4.savitar.tv/NBA/myStream/playlist.m3u8?wmsAuthSign=",
          img: "http://ustvgo.tv/wp-content/uploads/2019/01/nbatv-269x151.jpg",
          title: "NBA TV"
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
          src: source + this.auth
        }
        this.player.reset() // in IE11 (mode IE10) direct usage of src() when <src> is already set, generated errors,
        this.player.src(video)
        this.player.play()
      },
      getAuth(){
        axios.get('http://arlivetv.cf/getauth.php').then(({data}) => {
          this.auth = data.auth
          this.loading = false
        })
      }
    },
    mounted () {
      // this.playVideo(this.channels[0].src)
      this.getAuth()
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
