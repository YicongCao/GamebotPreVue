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
      console.log("you sent msg:" + newmessage);
      this.message = {
        id: 1,
        author: "Bot",
        contents: "为你推荐塞尔达传说",
        imageUrl:
          "https://img01.vgtime.com/photo/web/160616170441201.jpg?x-oss-process=image/resize,m_lfit,w_200",
        date: "16:30"
      };
    }
  }
};
</script>

<style>
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
