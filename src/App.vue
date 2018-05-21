<template>
  <v-app v-if="!theme">
  <v-navigation-drawer
      fixed
      :clipped="clipped"
      v-model="drawer"
      app
  >
    <v-list>
      <v-list-tile>
        <v-list-tile-title>Book of Mormon</v-list-tile-title>
      </v-list-tile>
      <div v-for="(book,i) in fatty">
        <v-list-group
          group
          lazy
          no-action

        >

          <v-list-tile inactive="true" slot="activator" @click="show = true">
            <div>{{ book.book }}</div>
          </v-list-tile>
          <v-list-tile
            v-if="show"
            v-for="(chapter, i) in book.chapters"
            :key="i"
            @click="getChapter(book.book, (i+1))"
          >
            <div>{{chapter}}</div>
          </v-list-tile>
        </v-list-group>
      </div>
    </v-list>
  </v-navigation-drawer>


    <v-toolbar fixed app :clipped-left="clipped">
      <v-toolbar-side-icon @click.stop="drawer = !drawer"></v-toolbar-side-icon>
      <v-btn icon @click.stop="clipped = !clipped">
        <v-icon>web</v-icon>
      </v-btn>
      <v-toolbar-title v-text="title"></v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn class="primary" @click="darken">Dark Theme</v-btn>
    </v-toolbar>
    <v-content>
      <v-container fluid>
        <v-slide-y-transition mode="out-in">
          <v-layout row wrap justify-center>
            <v-flex  xs5 sm5 md5 lg5>
              <h4 class="headline text-md-center">{{ bookTitle}}</h4>
              <br>
              <h4 class="headline text-md-center">{{ chapterTitle }}</h4>
              <br>  
              <div v-for="(verse,i) in chapter.verses">
              <p>
                <span class="title">{{ verse.verse }}</span>
                <span class="subheading">{{ verse.text }}</span>
              </p>
              <br>
            </div>
            </v-flex>
          </v-layout>
        </v-slide-y-transition>
      </v-container>
    </v-content>

    <v-footer :fixed="fixed" :clipped-left="clipped" app>
      <span>&copy; 2017</span>
    </v-footer>
  </v-app>
</template>

<script>
import axios from 'axios'
import Vue from 'vue'
  export default {
    data () {
      return {
        clipped: false,
        drawer: true,
        fixed: false,
        miniVariant: false,
        right: true,
        rightDrawer: false,
        title: 'Book of Mormon',
        fatty: [],
        chapter: {},
        book_chapter: '',
        theme: false,
        show: false
      }
    },
    mounted () {
        this.getData()
    },
    methods: {
      darken () {
        this.theme = true
      },
      getData () {
        var fat = []
        axios.get('http://localhost:5000/books')
        .then(function (response) {
          response.data.forEach(element => {
            var chapters = new Array(element.chapters)
            for (var i = 0, len = chapters.length; i < len; i++) {
              chapters[i] = element.book + ' '+ (i + 1)
            }
            fat.push({'book':element.book,'chapters':chapters})
          });
        })
        .catch(function (error) {
          console.log(error);
        });
        this.fatty = fat
      },
      getChapter (book, chapter) {
        var chap = {}
        axios.get('http://localhost:5000/' + book + '/' + chapter)
          .then((response) => {
              this.chapter = null
              this.chapter = response.data
              this.bookTitle = book
              this.chapterTitle = 'Chapter ' + chapter
          })
          .catch(function (error) {
              console.log(error);
          });
        }
        
      }
  }
</script>
