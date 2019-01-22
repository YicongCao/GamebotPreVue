<template>
  <div id="app">
    <basic-vue-chat
      :new-message="message"
      :title="'Gamebot Demo'"
      @newOwnMessage="onNewOwnMessage"
    />
    <!-- <router-view/> -->
  </div>
</template>

<script>
import BasicVueChat from "basic-vue-chat";

export default {
  name: "App",
  components: {
    BasicVueChat
  },
  data: function() {
    return {
      message: {}
    };
  },
  methods: {
    onNewOwnMessage: function(newmessage) {
      const vm = this;
      console.log("1st you sent msg:" + newmessage);
      var http = require("http");
      var options = {
        host: "",
        path: "http://127.0.0.1:5005/parse?q=" + encodeURIComponent(newmessage)
      };
      var req = http.get(options, function(res) {
        console.log("STATUS: " + res.statusCode);
        var bodyChunks = [];

        res
          .on("data", function(chunk) {
            bodyChunks.push(chunk);
          })
          .on("end", function() {
            var body = Buffer.concat(bodyChunks);
            //console.log("BODY: " + body + "\r\n");

            // make second req

            var nlu = JSON.parse(body);
            console.log(nlu);
            var entities = {};
            console.log("INTENT: " + nlu.intent.name);
            for (var entity in nlu.entities) {
              entities[nlu.entities[entity].entity] =
                nlu.entities[entity].value;
              console.log(
                "ENTITY: " +
                  nlu.entities[entity].entity +
                  " -> " +
                  nlu.entities[entity].value
              );
            }
            console.log("FILTER: ");
            console.log(entities);

            var filter = new Object();
            filter.query = entities;
            console.log("2nd request: " + JSON.stringify(filter));

            var http2 = require("http");
            var post_data = JSON.stringify(filter);
            var options2 = {
              host: "",
              path:
                "http://127.0.0.1:5010/gamesearch?query=" +
                encodeURIComponent(post_data),
              headers: {
                "Access-Control-Allow-Credentials": true,
                "Access-Control-Allow-Origin": "*",
                "Access-Control-Allow-Methods": "GET",
                "Access-Control-Allow-Headers": "Content-Type"
              }
            };
            var req2 = http.get(options2, function(res) {
              console.log("STATUS: " + res.statusCode);
              var bodyChunks = [];
              res
                .on("data", function(chunk) {
                  bodyChunks.push(chunk);
                })
                .on("end", function() {
                  var body = Buffer.concat(bodyChunks);
                  //console.log("BODY: " + body + "\r\n");

                  var games = JSON.parse(body);

                  console.log(games);

                  console.log("First: " + games.games[0].name);
                  console.log("With Pic: " + games.games[0].img);
                  //console.log(nlu.intent.name);

                  var respA = "";
                  var respB = "";

                  if (nlu.intent.name == "game_recommend") {
                    respA = "为你推荐" + games.games[0].name;
                  } else if (nlu.intent.name == "game_query_year") {
                    respA =
                      games.games[0].name + "于" + games.games[0].date + "推出";
                  } else if (nlu.intent.name == "game_query_company") {
                    respA =
                      games.games[0].name +
                      "是" +
                      games.games[0].company +
                      "出的游戏";
                  } else if (
                    nlu.intent.name == "game_query_rating" ||
                    nlu.intent.name == "game_query_review"
                  ) {
                    respA =
                      games.games[0].name +
                      "的评分是 " +
                      games.games[0].score +
                      " 分";
                  } else if (nlu.intent.name == "game_query_platform") {
                    respA =
                      games.games[0].name +
                      "支持 " +
                      games.games[0].platform +
                      " 这几个平台";
                  }
                  if (games.games.length > 5) {
                    respB = "其他相关游戏：\r\n";
                    respB += games.games[1].name + "\r\n";
                    respB += games.games[2].name + "\r\n";
                    respB += games.games[3].name + "\r\n";
                    respB += games.games[4].name + "\r\n";
                  }

                  var nowtime = new Date();
                  var timestr =
                    nowtime.getHours() +
                    ":" +
                    nowtime.getMinutes() +
                    ":" +
                    nowtime.getSeconds();

                  vm.message = {
                    id: 1,
                    author: "Bot",
                    contents: respA,
                    imageUrl: games.games[0].name.img,
                    date: timestr
                  };
                  console.log("\r\n----------DONE----------\r\n\r\n");
                });
            });
          });
      });
      // this.message = {
      //   id: 1,
      //   author: "Bot",
      //   contents: "为你推荐塞尔达传说：旷野之息",
      //   imageUrl: "https://img01.vgtime.com/photo/web/160616170441201.jpg",
      //   date: "16:30"
      // };
    }
  }
};
</script>

<style lang="scss">
@import "./assets/scss/main.scss";
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 10px;
  margin-left: 7px;
}
</style>
