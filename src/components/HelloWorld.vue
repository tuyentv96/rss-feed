<template>
  <div class="hello">
    <div v-for="item in articles" v-bind:key="item.id">
      <h3>{{ item }}</h3>
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
let urls = [];
let Parser = require("rss-parser");
let parser = new Parser({});
let articles = [];

function fetchData() {
  console.log("clicked");
  var ref = firebase.database().ref("rss/urls");
  ref.on("value", function(snapshot) {
    snapshot.forEach(function(childSnapshot) {
      var childData = childSnapshot.val();
      urls.push(childData);
      console.log(childData);
      (async () => {
        const data = await fetch(
          "https://tuyentv-cors.herokuapp.com/" + childData,
          {
            mode: "cors"
          }
        );
        const text = await data.text();
        parser.parseString(text, (err, parsed) => {
          parsed.items.forEach(function(entry) {
            console.log(entry.title + ":" + entry.link);
            articles.push({
              title: entry.title,
              link: entry.link
            });
          });
        });
      })();
    });
  });
}

fetchData();
export default {
  name: "HelloWorld",
  props: {
    msg: String
  },
  data() {
    return {
      articles: articles
    };
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
