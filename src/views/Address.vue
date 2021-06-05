<template>
  <div>
    <v-col>
      <v-sheet
        min-height="90vh"
        rounded="lg"
        style="padding-left: 20px; padding-top: 20px"
      >
        <v-col>
          <div style="font-size: 1.5em; font-weight: bold">RSS3: Address</div>
          <p>{{ id }}</p>
        </v-col>
        <v-col>
          <div style="font-size: 1.5em; font-weight: bold">RSS3: Items</div>
          <p>Summary: {{ items[0].summary }}</p>
          <p>ID: {{ items[0].id }}</p>
          <p>Published: {{ items[0].date_published }}</p>
          <p>Modified: {{ items[0].date_modified }}</p>
        </v-col>
        <v-col v-if="showcontents">
          <div style="font-size: 1.5em; font-weight: bold">RSS3: Content</div>
          <p>IPFS: {{ items[0].contents[0].address[0] }}</p>
          <p>File Type: {{ items[0].contents[0].mime_type }}</p>
          <p>Size: {{ items[0].contents[0].size_in_bytes }}</p>

          <vue-pdf-app v-if="items[0].contents[0].mime_type === 'application/pdf'" :pdf="items[0].contents[0].address[0]" />
        </v-col>
        <v-col>
          <v-card class="mx-auto" color="#00c700" dark max-width="400">
            <v-card-title>
              <v-icon large left> mdi-account </v-icon>
              <span class="font-weight-light">RSS3: Profile</span>
            </v-card-title>
            <v-card-text
              style="font-size: 1.5em"
              class="font-size-15 font-weight-bold"
            >
              {{ profile.bio }}
            </v-card-text>
            <v-card-actions>
              <v-list-item class="grow">
                <v-list-item-avatar color="grey darken-3">
                  <v-img
                    class="elevation-6"
                    :alt="profile.name"
                    :src="profile.avatar[0]"
                  ></v-img>
                </v-list-item-avatar>
                <v-list-item-content>
                  <v-list-item-title>{{ profile.name }}</v-list-item-title>
                </v-list-item-content>
                <v-row align="center" justify="end">
                  <v-icon class="mr-1"> mdi-heart </v-icon>
                  <span class="mr-1">·</span>
                  <v-icon class="mr-1"> mdi-share-variant </v-icon>
                </v-row>
              </v-list-item>
            </v-card-actions>
          </v-card>
        </v-col>
      </v-sheet>
    </v-col>
  </div>
</template>

<script>
import VuePdfApp from "vue-pdf-app";
// import this to use default icons for buttons
import "vue-pdf-app/dist/icons/main.css";

export default {
  components: {
    VuePdfApp,
  },
  data: () => ({
    id: "",
    version: "",
    date_created: "",
    date_updated: "",
    items: [
      {
        summary: "",
        id: "",
        date_published: "",
        date_modified: "",
        contents: [
          {
            address: [],
            mime_type: "",
            size_in_bytes: "",
          },
        ],
      },
    ],
    profile: {
      avatar: [],
      bio: "",
      name: "",
      signature: "",
    },
    signature: "",
    showcontents: false,
  }),
  async mounted() {
    this.id = this.$route.params.address;
    await this.axios.get("https://hub.rss3.io/" + this.id).then((response) => {
      this.id = response.data.id;
      this.version = response.data.version;
      this.date_created = response.data.date_created;
      this.date_updated = response.data.date_updated;
      this.items = response.data.items;
      this.profile = response.data.profile;
      this.signature = response.data.signature;
    });
    for (let i in this.items) {
      if (this.items[i].contents) {
        this.showcontents = true;
      }
    }
  },
};
</script>
