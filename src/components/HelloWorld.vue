<template>
  <div class="hello">
    <div v-for="item in articles" v-bind:key="item.id">
      <a v-bind:href="item.link">{{item.title}}</a>
      <el-divider></el-divider>
    </div>
  </div>
</template>

<script>
import firebase from "firebase";
// import axios from "axios";
// Your web app's Firebase configuration
var firebaseConfig = {
  apiKey: "AIzaSyCruMO0xdKBm7OV6oDiw7cfbP4w0YjEeKQ",
  authDomain: "rss-reader-ea442.firebaseapp.com",
  databaseURL: "https://rss-reader-ea442.firebaseio.com",
  projectId: "rss-reader-ea442",
  storageBucket: "rss-reader-ea442.appspot.com",
  messagingSenderId: "591950448463",
  appId: "1:591950448463:web:db9e35b3a159433531a73a",
  measurementId: "G-WNCMR5WGQE"
};
// Initialize Firebase
firebase.initializeApp(firebaseConfig);
firebase.analytics();
let Parser = require("rss-parser");
let parser = new Parser({});
let articles = [];

async function listRssUrl() {
  return new Promise(resolve => {
    let urls = [];
    var ref = firebase.database().ref("rss/urls");
    ref.on("value", function(snapshot) {
      snapshot.forEach(function(childSnapshot) {
        var childData = childSnapshot.val();
        urls.push(childData);
      });
      return resolve(urls);
    });
  });
}

async function listArticles(urls) {
  let atrs = await Promise.all(
    urls.map(async url => {
      const data = await fetch("https://tuyentv-cors.herokuapp.com/" + url, {
        mode: "cors"
      });
      const text = await data.text();
      return new Promise(resolve => {
        parser.parseString(text, (err, parsed) => {
          let arr = [];
          parsed.items.forEach(function(entry) {
            // console.log(entry.title + ":" + entry.link + ":" + entry.pubDate);
            arr.push({
              title: entry.title,
              link: entry.link,
              pubDate: entry.pubDate
            });
          });
          console.log(arr);
          return resolve(arr);
        });
      });
    })
  ).then(r => r.sort((a, b) => new Date(b.pubDate) - new Date(a.pubDate)));

  return atrs;
}

async function RenderFeed() {
  let u = await listRssUrl();
  let attr = await listArticles(u);
  let i;
  for (i in attr) {
    articles.push(...attr[i]);
  }
  await articles.sort(function(a, b) {
    return new Date(b.pubDate) - new Date(a.pubDate);
  });

  console.log(articles);
  return Promise.resolve(articles);
}

RenderFeed();
export default {
  data() {
    return {
      articles: articles
    };
  }
};
</script>

