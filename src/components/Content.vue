<template>
  <div class="main">
    <div class="dim"></div>
    <div class="modal">
      <h3>회원가입</h3>
      <div class="notice">
        지금 가입하면 꿈꾸던 기업에 재직중인 현직자와 <br>
        <strong>익명</strong>으로 대화할 수 있습니다.
      </div>
      <ul class="social-auth">
        <li class="facebook">페이스북 계정으로 회원가입</li>
        <li class="google">구글 계정으로 회원가입</li>
        <li class="naver">네이버 계정으로 회원가입</li>
      </ul>
      <button class="close-btn" @click="closeModal">나중에 하기</button>
    </div>
    <div class="jumbotron">
      <h1 class="display-4">{{article.title}}</h1>
      <p class="lead">{{article.contents}}</p>
      <hr class="my-4">
      <p>{{article.email}} / {{article.updated_at}}</p>
      <a class="btn btn-primary btn-lg" href="#" role="button">Learn more</a>
    </div>
    <div class="list-group">
      <a href="#" v-for="(reple) in replies" class="list-group-item list-group-item-action flex-column align-items-start">
        <div class="d-flex w-100 justify-content-between">
          <h5 class="mb-1">{{reple.email}}</h5>
          <small>{{reple.updated_at}}</small>
        </div>
        <p class="mb-1">{{reple.contents}}</p>
      </a>
    </div>
    <div class="snackbar">
      <button class="close-btn" @click="closeSnackbar">X</button>
      <div class="notice">
        지금 가입하면 꿈꾸던 기업에 재직중인 현직자와 <strong>익명</strong>으로 대화할 수 있습니다.
        <div class="web">
          <button class="sign-up">SNS계정으로 빠른 회원가입</button>
          또는
          <button class="login">로그인</button>
        </div>
        <div class="mobile">
          <button class="sign-up">회원가입</button>
        </div>

      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'content',
    data() {
      return {
        contentId: window.location.href.split('contents/')[1].substring(0, 1),
        article: {},
        replies: []
      }
    },
    methods: {
      getContent(contentId) {
        this.$http.get(`http://comento.cafe24.com/detail.php?req_no=${contentId}`)
          .then(res => {
            this.article = res.data.detail.article;
            this.replies = res.data.detail.replies;
            console.log(this.replies);
          })
      },
      showModal() {
        document.querySelector('.dim').classList.add('is-visible');
        document.querySelector('.modal').classList.add('is-visible');
      },
      closeModal() {
        document.querySelector('.dim').classList.remove('is-visible');
        document.querySelector('.modal').classList.remove('is-visible');
        document.querySelector('.snackbar').classList.add('is-visible');
      },
      closeSnackbar() {
        document.querySelector('.snackbar').classList.remove('is-visible');
      }
    },
    mounted() {
      this.getContent(this.contentId);
      setTimeout(this.showModal, 1500);
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
    transition: 1s;
    -webkit-transition: 1s;
  }

  .dim.is-visible {
    display: block;
  }

  .modal {
    top: 30px;
    display: none;
    opacity: 0;
    margin: 30px auto;
    width: auto;
    max-width: 500px;
    position: fixed;
    z-index: 10;
    background-color: #FFFFFF;
    flex-direction: column;
    -webkit-transition: opacity 1s ease-in;
    -moz-transition: opacity 1s ease-in;
    -ms-transition: opacity 1s ease-in;
    -o-transition: opacity 1s ease-in;
    transition: opacity 1s ease-in;
    text-align: center;
  }

  .modal.is-visible {
    opacity: 1;
    display: flex;
  }

  .modal h3 {
    margin: 20px auto;
    display: inline-block;
    width: 200px;
  }

  .modal .notice {
    border: solid 1px;
    width: 90%;
    margin: 10px auto;
    padding: 10px;
  }

  .notice {
    font-weight: bold;
  }

  .notice strong {
    color: #f95045;
  }

  .modal li {
    width: 90%;
    margin: 10px auto;
    display: block;
    color: #FFFFFF;
    padding: 10px 0;
  }

  .modal li:hover {
    opacity: 0.8;
    cursor: pointer;
  }

  .modal li.facebook {
    background-color: #4267b2;
  }

  .modal li.google {
    background-color: #d93025;
  }

  .modal li.naver {
    background-color: #42c729;
  }

  .modal .close-btn {
    padding: 10px 0;
    margin: 10px auto;
    width: 90%;
    background: none;
    border: none;
    font-size: 17px;
    font-weight: bold;
  }

  .modal .close-btn:hover {
    opacity: 0.8;
    background-color: #EFEFEF;
    cursor: pointer;
  }

  .snackbar {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
    background-color: #FFFFFF;
    text-align: center;
    border: solid #DDDDDD;
    padding: 20px 0;
    transform: translateY(100%);
    trainsition: transform 1s;
    z-index: 10;
  }

  .snackbar.is-visible {
    transform: translateY(0);
  }

  .snackbar .close-btn {
    position: absolute;
    top: -15px;
    right: 0;
    padding: 5px;
    background-color: #FFFFFF;
    border: none;
    color: #000000;
    width: 30px;
  }

  .snackbar .close-btn:hover {
    opacity: 0.8;
    background-color: #efefef;
    cursor: pointer;
  }

  .snackbar .notice {
    margin: 0 60px;
  }

  .snackbar button {
    background-color: #00c854;
    border: solid 1px #000000;
    color: #FFFFFF;
    margin: 15px 5px;
    width: 200px;
    padding: 5px 0;
  }

  .snackbar button:hover {
    cursor: click;
  }

  .snackbar .login {
    background-color: #dddddd;
    color: #000000;
  }

  .snackbar .mobile .sign-up {
    margin: 10px 0;
    width: 100%;
  }

  @media (min-width: 480px) {
    .mobile {
      display: none;
    }
  }

  @media (max-width: 480px) {
    .web {
      display: none;
    }
  }
</style>
