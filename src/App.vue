<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
  #app {
    margin: 15px;
  }

  .ad {
    display: inline-block;
    margin: 0 25px;
    box-sizing: border-box;
    text-align: -webkit-match-parent;
    list-style-type: none;
  }

  .ad .card-subject {
    padding: 20px 10px 10px 20px;
  }

  .ad .card-body {
    display: flex;
    padding-top: 0;

  }

  .ad .image {
    background-color: black;
    flex: 1;
    margin-right: 30px;
  }

  .ad .text {
    flex: 2;
  }

  @media (max-width: 480px) {
    .ad .card-body {
      flex-direction: column;
    }

    .ad .image {
      width: 100%;
      height: 200px;
      margin-bottom: 15px;
    }
  }


</style>
