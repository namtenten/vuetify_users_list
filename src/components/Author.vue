<template>
  <v-container fluid>
    <v-row class="text-left">
      <v-col
        class="mb-1"
        cols="12"
      >
        <v-card>
          <v-img
            height="200px"
            :src="require('../assets/images/green_grasses.jpg')"
          >
            <v-app-bar
              flat
              color="rgba(0, 0, 0, 0)"
              v-if="false"
            >
              <v-app-bar-nav-icon color="white"></v-app-bar-nav-icon>

              <v-toolbar-title class="title white--text pl-0">
                Messages
              </v-toolbar-title>

              <v-spacer></v-spacer>

              <v-btn
                color="white"
                icon
              >
                <v-icon>mdi-dots-vertical</v-icon>
              </v-btn>
            </v-app-bar>

            <v-card-title class="white--text mt-10">
              <v-avatar size="56">
                <img
                  alt="user"
                  :src="author.image"
                >
              </v-avatar>
              <p class="ml-3">
                {{author.name}}
              </p>
            </v-card-title>
          </v-img>

          <v-card-title>
            🌟🌟🌟🌟🌟
          </v-card-title>

          <v-card-subtitle>
            Beautiful song...
          </v-card-subtitle>

          <v-card-title>
            Social Medias
          </v-card-title>

          <v-card-subtitle>
            <div v-if="author.social_media.linkedin">
              LinkedIn: <a :href="author.social_media.linkedin">{{author.social_media.linkedin}}</a>
            </div>
            <div v-if="author.social_media.github">
              GitHub: <a :href="author.social_media.github">{{author.social_media.github}}</a><br>
            </div>
          </v-card-subtitle>


        </v-card>

      </v-col>
    </v-row>
  </v-container>
</template>

<script>
//  import Vue from 'vue'

  export default {
    name: 'Author'

    ,components: {
    }

    ,data: () => ({
      author : {
          name: 'NGUYEN NGOC NAM',
          image: 'https://avatars1.githubusercontent.com/u/19430488?s=460&u=9178a079c227a46ee6b8dc598fa2cf6cdbbde4a1&v=4',
          social_media:{
            linkedin: '',
            github: 'https://github.com/namtenten',
          }
        }
    })

    ,methods: {
      initialise() {
      }

    }
  }
</script>
