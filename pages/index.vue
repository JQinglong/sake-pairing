<template>
  <v-card>
    <v-card-title class="headline"> 今の季節の「日本酒に合う」</v-card-title>
    <v-card-text>
      <wordcloud
        :data="tweetword"
        name-key="name"
        value-key="value"
        :show-tooltip="false"
      />
    </v-card-text>
    <v-card-text>
      <v-row>
        <v-col v-for="tweet in tweets" :key="tweet.id" cols="2">
          <v-hover>
            <template v-slot:default="{ hover }">
              <v-card>
                <v-img
                  :src="tweet.entities.media[0].media_url_https"
                  class="white--text align-end"
                  gradient="to bottom, rgba(0,0,0,.1), rgba(0,0,0,.5)"
                  height="200px"
                >
                  <!-- <v-card-title v-text="tweet.text"></v-card-title> -->
                </v-img>
                <v-card-text class="text--primary">
                  <!-- <div>{{ tweet.text }}</div> -->
                </v-card-text>
                <v-fade-transition>
                  <v-overlay v-if="hover" absolute color="#036358">
                    {{ tweet.text.split('https://t.co')[0] }}
                    <v-btn icon @click="externalLink('https://t.co'+tweet.text.split('https://t.co')[1])">
                      <v-icon>mdi-twitter</v-icon>
                    </v-btn>
                  </v-overlay>
                </v-fade-transition>
              </v-card>
            </template>
          </v-hover>
        </v-col>
      </v-row>
    </v-card-text>
  </v-card>
</template>

<script>
import axios from "axios";
export default {
  data: () => ({
    myColors: ["#38b508", "#76ed47", "#a8e88f", "#39c900"],
    tweetword: [
      { name: "日本酒に合う", value: "100" },
      { name: "ペアリング", value: "10" },
    ],
    tweets: null,
    hover: false,
  }),
  mounted() {
    const pairing_url =
      "https://us-central1-jq-apps-1568264503782.cloudfunctions.net/sake-pairing-api";
    axios
      .get(pairing_url)
      .then((response) => (this.tweetword = JSON.parse(response.data.result)));
    const tweets_url =
      "https://us-central1-jq-apps-1568264503782.cloudfunctions.net/sake-tweet-api";
    axios
      .get(tweets_url)
      .then((response) => (this.tweets = response.data.result));
  },
  methods: {
    externalLink(url) {
      window.location.href = url;
    },
  },
};
</script>
<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}
</style>