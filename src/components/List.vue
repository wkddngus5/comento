<template>
  <div class="main">
    <div class="dim"></div>
    <div class="modal">
      <div class="modal-header">
        <h3>필터</h3>
        <button @click="closeModal">X</button>
      </div>

      <ul class="filters">
        <li class="filter" v-for="(filter) in filters">
          <label>카테고리{{filter}}</label>
          <input type="checkbox" v-bind:data-item="filter" checked="checked" @click="setTempVisibles(filter)">
        </li>
      </ul>
      <button @click="saveFilter">저장</button>
    </div>
    <div class="header">
      <button type="button" class="btn btn-primary" @click="showModal">필터</button>
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
      <li class="content" v-for="(content) in contents" v-if="visibles.includes(parseInt(content.category_no))">
        <div class="card">
          <div class="card-header">
            <h6 class="card-subtitle mb-2 text-muted">카테고리{{ content.category_no }}</h6>
            <div class="filler"></div>
            <h6 class="card-subtitle mb-2 text-muted">NO. {{ content.no }}</h6>
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
  const CONTENTS_LIMIT = 300;
  let ticking = false;

  export default {
    name: 'list',
    data() {
      return {
        contents: [],
        filters: [1, 2, 3],
        tempVisibles: [],
        visibles: [1, 2, 3],
        ord: 'asc',
        page: 1
      }
    },
    methods: {
      getContents(page, ord) {
        ticking = false;
        this.$http.get(`http://comento.cafe24.com/request.php?page=${page}%B8&ord=${ord}`)
          .then((res) => {
            for(const index in res.data.list) {
              let contentBody = res.data.list[index].contents;
              if(contentBody.length > CONTENTS_LIMIT) {
                res.data.list[index].contents = contentBody.substring(0, CONTENTS_LIMIT) + '······.';
              }
              res.data.list[index].subject = 'article';
            }
            this.contents = this.contents.concat(res.data.list);
            this.page++;
            ticking = true;
          })
      },
      resetAdds() {
        const contentList = document.querySelectorAll('.content');
        for(const index in contentList) {
          if(index % 4 === 3) {
            contentList[index].insertAdjacentHTML('afterend', '<hr>');
          }
        }
      },
      changeOrder(ord) {
        this.ord = ord;
        this.page = 1;
        this.contents = [];
        this.getContents(this.page, this.ord);
      },
      showModal() {
        this.tempVisibles = [];
        Object.assign(this.tempVisibles, this.visibles);
        document.querySelector('.dim').classList.add('is-visible');
        document.querySelector('.modal').classList.add('is-visible');
      },
      closeModal() {
        document.querySelector('.dim').classList.remove('is-visible');
        document.querySelector('.modal').classList.remove('is-visible');
      },
      setTempVisibles(no) {
        if(this.tempVisibles.includes(no)) {
          this.tempVisibles.splice(this.tempVisibles.indexOf(no), 1);
        } else {
          this.tempVisibles.push(no);
        }
      },
      saveFilter() {
        const save = confirm('해당 필터를 적용하시겠습니까?');
        if(save) {
          this.visibles = [];
          Object.assign(this.visibles, this.tempVisibles);
          this.closeModal();
        }
      }
    },
    mounted() {
      this.getContents(this.page, this.ord);
      window.onscroll = () => {
        if (ticking && (window.innerHeight + window.scrollY) >= document.body.offsetHeight) {
          this.getContents(this.page, this.ord);
        }
      };
    },
    updated() {
      this.resetAdds();
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

  .dim {
    display: none;
    width: 100vw;
    height: 100vh;
    background-color: #000000;
    position: fixed;
    z-index: 5;
    opacity: 0.5;
  }

  .dim.is-visible {
    display: block;
  }

  .modal {
    margin: 10%;
    display: none;
    width: auto;
    height: 200px;
    position: fixed;
    z-index: 10;
    background-color: #FFFFFF;
    flex-direction: column;
  }

  .modal.is-visible {
    display: flex;
  }

  .modal h3 {
    display: inline-block;
    width: 200px;
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
