<template>
  <div class="box-incorrect">
    <figure class="m-0">
      <img
        src="../../public/images/undraw_winners_ao2o2.svg"
        alt=""
        class="m-auto"
      />
    </figure>
    <h2 class="text-center p-5">Resultados</h2>
    <p class="m-0 text-center">
      Tú tienes <span>{{ totalCorrects }}</span> respuestas correctas.
    </p>
    <div class="mx-auto text-center mt-5 mb-3">
      <button class="p-3" @click="tryAgain()">Intentar otra vez</button>
    </div>
  </div>
</template>

<script>
  export default {
    name: "IncorrectComponent",
    created() {},
    props: ["numCorrects"],
    data() {
      return {};
    },
    watch: {},
    methods: {
      tryAgain() {
        this.$emit("tryAgain", true);
      },
    },
    computed: {
      totalCorrects() {
        return this.numCorrects;
      },
    },
  };
</script>

<style lang="scss" scoped>
  h2 {
    color: #1d355d;
    font-size: 28px;
    font-weight: 700;
  }
  p {
    color: #1d355d;
    font-size: 16px;
    font-weight: normal;
  }
  button {
    border: 2px solid #1d355d;
    border-radius: 12px;
    box-sizing: border-box;
    color: #1d355d;
  }
  button:hover {
    opacity: 0.9;
  }
  span {
    color: #6fcf97;
    font-size: 22px;
  }
</style>
