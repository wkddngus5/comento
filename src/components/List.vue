<template>
  <div class="main">
    <div class="dim"></div>
    <div class="modal">
      <div class="modal-header">
        <h3>필터</h3>
        <button class="close-btn" @click="closeModal">X</button>
      </div>

      <ul class="filters">
        <li class="filter" v-for="(filter) in filters">
          <label>{{filter.name}}</label>
          <input type="checkbox" v-bind:data-item="filter" checked="checked" @click="setTempVisibles(filter.no)">
        </li>
      </ul>
      <button class="save-btn" @click="saveFilter">저장</button>
    </div>
    <div class="header">
      <button type="button" class="btn btn-primary filter" @click="showModal">필터</button>
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
      <li class="content" v-for="(content) in contents" v-if="visibles.includes(parseInt([content.category_no]))">
        <a v-bind:href="'/#/contents/' + content.no">
          <div class="card">
            <div class="card-header">
              <h6 class="card-subtitle mb-2 text-muted">{{ content.category }}</h6>
              <div class="filler"></div>
              <h6 class="card-subtitle mb-2 text-muted">NO. {{ content.no }}</h6>
            </div>
            <div class="card-body">
              <h6 class="card-subtitle mb-2 text-muted">{{ content.email }} | {{ content.updated_at }}</h6>
              <h5 class="card-title">{{ content.title }}</h5>
              <p class="card-text">{{ content.contents }}</p>
            </div>
          </div>
        </a>
      </li>
    </ul>
  </div>
</template>

<script>
  const CONTENTS_LIMIT = 300;
  const AD_BY_CONTENTS = 4;
  const AD_ORDER = 3;

  let ticking = false;

  function summarizeContents(contents) {
    if (contents.length > CONTENTS_LIMIT) {
      return contents.substring(0, CONTENTS_LIMIT) + '······.';
    }
    return contents;
  }

  export default {
    name: 'list',
    data() {
      return {
        contents: [],
        ads: [],
        filters: [],
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
            for (const index in res.data.list) {
              const item = res.data.list[index];
              item.contents = summarizeContents(item.contents);
              item.subject = 'article';
              item.category = this.filters[parseInt(item.category_no) - 1].name;
            }
            this.contents = this.contents.concat(res.data.list);
            this.page++;
            ticking = true;
          })
      },
      getCategories() {
        return new Promise((resove, reject) => {
          this.$http.get(`http://comento.cafe24.com/category.php`)
            .then(res => {
              this.filters = res.data.list;
              resove();
            })
        });
      },
      getAds(page) {
        return new Promise((resolve, reject) => {
          this.$http.get(`http://comento.cafe24.com/ads.php?page=${page}`)
            .then(res => {
              for (const index in res.data.list) {
                console.log(res.data.list[index]);
                const item = res.data.list[index];
                item.contents = summarizeContents(item.contents);
                item.subject = 'ad';
              }
              this.ads = this.ads.concat(res.data.list);
              resolve();
            })
        });
      },
      resetAdds() {
        const contentList = document.querySelectorAll('.content');
        const adList = document.querySelectorAll('.ad');

        for(let index = 0 ; index < adList.length ; index++) {
          adList[index].remove();
        }

        for (let index = 0 ; index <  contentList.length ; index++) {
          if (index % AD_BY_CONTENTS === AD_ORDER) {
            const toGetAdOrder = parseInt(index / AD_BY_CONTENTS);
            if(!this.ads[toGetAdOrder]) {
              this.getAds(toGetAdOrder / 10 + 1).then(() => {
                contentList[index].insertAdjacentHTML('afterend', this.makeAdByTemplate(this.ads[toGetAdOrder]));
              })
            } else {
              contentList[index].insertAdjacentHTML('afterend', this.makeAdByTemplate(this.ads[toGetAdOrder]));
            }
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
        const intNumber = parseInt(no);
        if (this.tempVisibles.includes(intNumber)) {
          this.tempVisibles.splice(this.tempVisibles.indexOf(intNumber), 1);
        } else {
          this.tempVisibles.push(intNumber);
        }
      },
      saveFilter() {
        const save = confirm('해당 필터를 적용하시겠습니까?');
        if (save) {
          this.visibles = [];
          for(let i = 0 ; i < this.tempVisibles.length ; i++) {
            this.visibles.push(this.tempVisibles[i]);
          }
          this.closeModal();
        }
      },
      makeAdByTemplate(ad) {
        const imageUrl = `http://comento.cafe24.com/public/images/${ad.img}`;

        const adElm = `
        <li class="ad">
            <div class="card">
              <div class="card-subject">
                <h6 class="card-subtitle mb-2 text-muted">Sponsored</h6>
              </div>
              <div class="card-body">
                <div class="image" style="background-image: url(${imageUrl})"></div>
                <div class="text">
                  <h5 class="card-title">${ad.title}</h5>
                  <p class="card-text">${ad.contents}</p>
                </div>
              </div>
            </div>
          </li>
        `
        return adElm;
      }
    },
    mounted() {
      this.getCategories().then(() => {
        this.getContents(this.page, this.ord);
      });
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

  a:hover {
    text-decoration: none;
    opacity: 0.8;
  }

  .dim {
    top: 0;
    left: 0;
    display: none;
    width: 100vw;
    height: 100vh;
    background-color: #000000;
    position: fixed;
    z-index: 5;
    opacity: 0.7;
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
    text-align: center;
  }

  .modal.is-visible {
    display: flex;
  }

  .modal .close-btn {
    background: none;
    border: none;
  }

  .modal .close-btn:hover {
    cursor: pointer;
    opacity: 0.8;
    background-color: #efefef;
  }

  .modal h3 {
    display: inline-block;
    width: 200px;
    margin: 0 auto;
  }

  .modal .save-btn {
    background: none;
    width: 300px;
    margin: 0 auto;
  }

  .modal .save-btn:hover {
    cursor: pointer;
    opacity: 0.8;
    background-color: #efefef;
  }

  .btn.filter {
    margin: 0 0 0 40px;
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
