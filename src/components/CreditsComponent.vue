<template>
  <div class="credits">
    <div class="container pt-14">
      <p class="m-0 text-center ">
        Created by
        <a href="https://github.com/FannyFarroF" target="_blank" class="me"
          >Fanny Farro
        </a>
        -
        <a href="https://devchallenges.io/" class="">devChallenges.io</a>
      </p>
    </div>
  </div>
</template>

<script>
  export default {
    name: "CreditsComponent",
    created() {},
    data() {
      return {};
    },
    props: {},
    methods: {},
  };
</script>

<style lang="scss" scoped>
  .credits {
    p {
      color: #f2f2f2;
      font-size: 14px;
      a {
        color: #f2f2f2;
        text-decoration: none;
      }
      .me {
        font-weight: bold;
      }
    }
  }
</style>
