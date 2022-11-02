<template>
  <!DOCTYPE html>
  <html lang="">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <link rel="icon" href="<%= BASE_URL %>vite.svg">
    <link rel="stylesheet" href="https://www.w3schools.com/w3css/4/w3.css">
    <title></title>
  </head>
  <body>

  <div id="app"></div>
  <!-- built files will be auto injected -->
  </body>
  </html>
  <div class="board-detail">
    <div class="common-buttons">
      <button type="button" class="w3-button w3-round w3-blue-gray" v-on:click="fnSave">저장</button>&nbsp;
      <button type="button" class="w3-button w3-round w3-gray" v-on:click="fnList">목록</button>
    </div>
    <div class="board-contents">
      <input type="text" v-model="title" class="w3-input w3-border" placeholder="제목을 입력해주세요.">
      <input type="text" v-model="writer" class="w3-input w3-border" placeholder="작성자를 입력해주세요." v-if="id === undefined">
    </div>
    <div class="board-contents">
      <textarea id="" cols="30" rows="10" v-model="content" class="w3-input w3-border" style="resize: none;">
      </textarea>
    </div>
    <div class="common-buttons">
      <button type="button" class="w3-button w3-round w3-blue-gray" v-on:click="fnSave">저장</button>&nbsp;
      <button type="button" class="w3-button w3-round w3-gray" v-on:click="fnList">목록</button>
    </div>
  </div>
</template>

<script>
export default {
  data() { //변수생성
    return {
      requestBody: this.$route.query,
      id: this.$route.query.id,

      title: '',
      writer: '',
      content: '',
    }
  },
  mounted() {
    this.fnGetView()
  },
  methods: {
    fnGetView() {
      if (this.id !== undefined) {
        this.$axios.get(this.$serverUrl + '/board/' + this.id, {
          params: this.requestBody
        }).then((res) => {
          this.title = res.data.title
          this.writer = res.data.writer
          this.content = res.data.content
        }).catch((err) => {
          console.log(err)
        })
      }
    },
    fnList() {
      delete this.requestBody.id
      this.$router.push({
        path: './board',
        query: this.requestBody
      })
    },
    fnView(id) {
      this.requestBody.id = id
      this.$router.push({
        path: './detail',
        query: this.requestBody
      })
    },
    fnViewUpdate(id) {
      this.requestBody.idx = id
      this.$router.push({
        path: './detail',
        query: this.requestBody
      })
    },
    fnSave() {
      this.form = {
        "id": this.id,
        "title": this.title,
        "content": this.content,
        "writer": this.writer
      }
      console.log(this.writer, this.title, this.content);

      if (this.id === undefined) {
        let apiUrl = this.$serverUrl + '/board'
        //INSERT
        this.$axios.post(apiUrl, null, {params: this.form})
            .then((res) => {
              alert('글이 저장되었습니다.')
              this.fnView(res.data)
            }).catch((err) => {
          if (err.message.indexOf('Network Error') > -1) {
            alert('네트워크가 원활하지 않습니다.\n잠시 후 다시 시도해주세요.')
          }
        })
      } else {
        let apiUrl = this.$serverUrl + '/board/' + this.id
        //UPDATE
        this.$axios.put(apiUrl,null, {params: this.form})
            .then((res) => {
              alert('글이 저장되었습니다.')
              this.fnViewUpdate(res.data.id)
            }).catch((err) => {
          if (err.message.indexOf('Network Error') > -1) {
            alert('네트워크가 원활하지 않습니다.\n잠시 후 다시 시도해주세요.')
          }
        })
      }
    }
  }
}
</script>
<style scoped>

</style>