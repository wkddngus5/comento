<template>
  <div class="main">
    <div class="header">
      <button type="button" class="btn btn-primary">필터</button>
      <div class="filler"></div>
      <ul class="nav">
        <li class="nav-item">
          <a v-if="ord === 'asc'" class="nav-link active" href="#" @click="changeOrder('asc')">오름차순</a>
          <a v-else class="nav-link disabled" href="#" @click="changeOrder('asc')">오름차순</a>
        </li>
        <li class="nav-item">
          <a v-if="ord === 'desc'" class="nav-link active" href="#" @click="changeOrder('desc')">내림차순</a>
          <a v-else class="nav-link disabled" href="#" @click="changeOrder('desc')">내림차순</a>
        </li>
      </ul>
    </div>
    <ul>
      <li class="content" v-for="(content, index) in contents[0]">
        <div class="card">
          <div class="card-header">
            <h6 class="card-subtitle mb-2 text-muted">{{ content.category_no }}</h6>
            <div class="filler"></div>
            <h6 class="card-subtitle mb-2 text-muted">카테고리{{ content.no }}</h6>
          </div>
          <div class="card-body">
            <h6 class="card-subtitle mb-2 text-muted">{{ content.email }} | {{ content.updated_at }}</h6>
            <h5 class="card-title">{{ content.title }}</h5>
            <p class="card-text">{{ content.contents }}</p>
          </div>
        </div>
      </li>
    </ul>

  </div>
</template>

<script>
  export default {
    name: 'main',
    data() {
      return {
        contents: [],
        ord: 'asc'
      }
    },
    methods: {
      getContents(page, ord) {
        this.$http.get(`http://comento.cafe24.com/request.php?page=${page}%B8&ord=${ord}`)
          .then((res) => {
            console.log(res.data.list);
            this.contents.push(res.data.list);
          })
      },
      changeOrder(ord) {
        this.ord = ord;
      }
    },
    mounted() {
      this.getContents(1, 'asc');
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  h1, h2 {
    font-weight: normal;
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

  .header {
    display: flex;
    align-self: flex-end;
  }

  .filler {
    flex-grow: 1;
  }

  ul.nav > li {
    margin: 0;
  }

  ul.nav > li:hover {
    opacity: 0.7;
  }

  .card {
    margin: 15px;
  }

  .card-header {
    display: flex;
  }

</style>
