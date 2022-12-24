<template>
  <v-app>
    <v-main>
      <input type="text" id="name" v-model="channelName" placeholder="Enter Channel Name">
      <br/>
      <button class="button-29" role="button" @click="fetchChannelID">Search Channels</button>
      <v-container class="channel-container" v-if="channelIDs.length">
        <v-row wrap>
          <v-col xs12 sm6 md4 lg4 v-for="channel in channelIDs" :key="channel">
            <v-card flat class="text-xs-center">
              <v-responsive class="pt-4">
              </v-responsive>
              <v-card-text>
                <div class="subheading">
                  <h1>{{ channel.id }}</h1>
                </div><br>
                <div class="grey--text">
                    <img v-bind:src="channel.profilepicture">
                </div>
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </v-container><br/>
      <input type="text" id="video" v-model="channelID" placeholder="Enter the specified Channel ID">
      <br/>
      <button class="button-29" role="button" @click="fetchVideos">Fetch Videos</button>
      <v-container class="video-container" v-if="videos.length">
        <v-row wrap>
          <v-col xs12 sm6 md4 lg4 v-for="videoTitle in videos" :key="videoTitle.title">
            <v-card flat class="text-xs-center">
              <v-responsive class="pt-4">
              </v-responsive>
              <v-card-text>
                <div class="subheading">
                  <h1>{{ videoTitle.title }}</h1>
                </div><br>
                <div class="grey--text">
                  <a v-bind:href="videoTitle.videoID" target="_blank" rel="noreferrer">
                    <img v-bind:src="videoTitle.thumbnailURL">
                  </a>
                </div>
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      videos: [],
      channelIDs: [],
      channelID: "",
      channelName: ""
    }
  },
  methods: {
    fetchChannelID: function() {
      fetch("https://www.googleapis.com/youtube/v3/search?part=snippet&type=channel&maxResults=1&q=" + this.channelName + "&key=USEYOUROWNAPIKEY")
      .then((channelNameResults) => {
                return channelNameResults.json()
      }).then ((channelNameData) => {
        let channels = channelNameData.items
        let newObject = {}
        for (let item of channels) {
          newObject = {
            ...newObject,
            id: item.snippet.channelId,
            profilepicture: item.snippet.thumbnails.default.url
          }
          this.channelIDs.push(newObject)
        }
      })
    },
    fetchVideos: function() {
      fetch("https://youtube.googleapis.com/youtube/v3/search?part=snippet&channelId=" + this.channelID + "&maxResults=10&order=date&key=USEYOUROWNAPIKEY")
        .then((videoResults) => {
          return videoResults.json()
        }).then ((videoData) => {
          let videos = videoData.items
          let newObject = {}
          for (let item of videos) {
            if (item.id.kind != "youtube#channel") {
              newObject = {
                  ...newObject,
                  title: item.snippet.title,
                  thumbnailURL: item.snippet.thumbnails.high.url,
                  videoID: "https://www.youtube.com/watch?v=" + item.id.videoId
              }
              this.videos.push(newObject)
            }
            else{
              console.log("Ignore")
            }
        }
    })
  },
},
}
</script>

<style scoped>
/* CSS */
.button-29 {
  align-items: center;
  appearance: none;
  background-image: radial-gradient(100% 100% at 100% 0, #5adaff 0, #5468ff 100%);
  border: 0;
  border-radius: 6px;
  box-shadow: rgba(45, 35, 66, .4) 0 2px 4px,rgba(45, 35, 66, .3) 0 7px 13px -3px,rgba(58, 65, 111, .5) 0 -3px 0 inset;
  box-sizing: border-box;
  color: #fff;
  cursor: pointer;
  display: inline-flex;
  font-family: "JetBrains Mono",monospace;
  height: 48px;
  justify-content: center;
  line-height: 1;
  list-style: none;
  overflow: hidden;
  padding-left: 16px;
  padding-right: 16px;
  position: relative;
  text-align: left;
  text-decoration: none;
  transition: box-shadow .15s,transform .15s;
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  white-space: nowrap;
  will-change: box-shadow,transform;
  font-size: 18px;
}

.button-29:focus {
  box-shadow: #3c4fe0 0 0 0 1.5px inset, rgba(45, 35, 66, .4) 0 2px 4px, rgba(45, 35, 66, .3) 0 7px 13px -3px, #3c4fe0 0 -3px 0 inset;
}

.button-29:hover {
  box-shadow: rgba(45, 35, 66, .4) 0 4px 8px, rgba(45, 35, 66, .3) 0 7px 13px -3px, #3c4fe0 0 -3px 0 inset;
  transform: translateY(-2px);
}

.button-29:active {
  box-shadow: #3c4fe0 0 3px 7px inset;
  transform: translateY(2px);
}
</style>