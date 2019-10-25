<template>
  <div class="hello">
    <el-row>
      <el-button type="danger" @click="editvalue()">Danger</el-button>
    </el-row>
  </div>
</template>

<script>
import firebase from "firebase";
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
let parser = new Parser({
  headers: {
    mode: "cors"
  }
});

export default {
  name: "HelloWorld",
  props: {
    msg: String
  },
  methods: {
    editvalue() {
      console.log("clicked");
      var ref = firebase.database().ref("rss/urls");
      ref.on("value", function(snapshot) {
        snapshot.forEach(function(childSnapshot) {
          var childData = childSnapshot.val();
          urls.push(childData);
        });

        console.log(urls);
        (async () => {
          let feed = await parser.parseURL("https://quan-cam.com/rss");
          console.log(feed.title);

          feed.items.forEach(item => {
            console.log(item.title + ":" + item.link);
          });
        })();
      });
    }
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
