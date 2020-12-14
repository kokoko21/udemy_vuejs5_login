<template>
  <div>
    <router-view></router-view>
    <h3>掲示板に投稿する</h3>
    <label for="name">ニックネーム</label>
    <input id="name" type="text">
    <br><br>
    <label for="comment">コメント：</label>
    <textarea id="comment" v-model="comment"></textarea>
    <br><br>
    <button @click="createComment">コメントをサーバに送る</button>
    <h2>掲示板</h2>
    <div v-for="post in posts" :key="post.name">
      <br>
      <div>名前：{{post.fields.name.stringValue}}</div>
      <div>コメント：{{post.fields.comment.stringValue}}</div>
    </div>
    <br><hr><br>
    <router-view name="submit"></router-view>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      name: "",
      comment: "",
      posts: []
    };
  },
  computed: {
    idToken() {
      return this.$store.getters.idToken;
    }
  },
  created() { // リロード時
    axios.get( // url, リクエスト
      '/comments', {
        headers: {
          Authorization: `Bearer ${this.idToken}`
        }
      }
    )
    .then(response => {
      this.posts = response.data.documents;
      console.log(response.data.documents);
    });
  },
  methods: {
    createComment() {
      // データをサーバに送る（post）
      axios.post( // url, 送るデータ
        'https://firestore.googleapis.com/v1/projects/vuejs-axios-15c6b/databases/(default)/documents/comments',
        {
          fields: {
            name: {
              stringValue: this.name
            },
            comment: {
              stringValue: this.comment
            }
          }
        },{
        headers: {
          Authorization: `Bearer ${this.idToken}`
        }
      }
      )
      .then(response => { // axiosの処理が成功した時
        console.log(response);
      })
      .catch(error => { // axiosの処理が失敗した時
        console.log(error);
      });
      this.name = "";
      this.comment = "";
    }
  }
};
</script>




