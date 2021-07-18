<template>
  <div class="home">
    <div class="spinner" v-show="hayData == false">
      <div
        class="animate-spin rounded-full h-32 w-32 border-t-2 border-b-2 border-white-500"
      ></div>
    </div>
    <div class="container p-16" v-show="hayData == true">
      <div class="box w-6/12 m-auto relative">
        <h1 class="uppercase mb-4">country quiz</h1>
        <figure
          class="m-0 absolute right-0 img-correc"
          v-show="showComponentIncorrect == false"
        >
          <img src="../../public/images/undraw_adventure_4hum1.svg" alt="" />
        </figure>
        <div class="card bg-white  rounded ">
          <div class="wrapper py-4 px-8">
            <div class="box-correct" v-show="showComponentIncorrect == false">
              <figure class="mb-0 mt-10 img-band" v-show="indexQuestion == 1">
                <img :src="urlBan" alt="" srcset="" />
              </figure>
              <p
                class="mb-0 question"
                v-bind:class="indexQuestion == 1 ? 'mt-4' : 'mt-10'"
              >
                {{ indexQuestion == 1 ? "" : capital }}
                {{ question }}
              </p>
              <ol
                class="mt-4 
            "
              >
                <li
                  :id="index"
                  class="alternative mb-5 flex justify-between items-center hover"
                  v-for="(item, index) in alternativasDesordenadas"
                  :key="index"
                  @click="evaluateAnswer(item, index)"
                >
                  <p class="wrapper p-3 m-0">
                    <b class="mr-10">
                      {{ enumerationAlt[index] }}
                    </b>
                    {{ item }}
                  </p>
                </li>
              </ol>
              <div class="flex justify-end">
                <button
                  class="btn p-3"
                  v-show="correct == true"
                  @click="continuePlay()"
                >
                  Siguiente
                </button>
              </div>
            </div>
            <IncorrectComponent
              v-show="showComponentIncorrect == true"
              v-bind:numCorrects="numCorrects"
              v-on:tryAgain="tryAgain($event)"
            />
          </div>
        </div>
      </div>

      <CreditsComponent />
    </div>
  </div>
</template>

<script>
  // @ is an alias to /src
  import axios from "axios";
  import IncorrectComponent from "@/components/IncorrectComponent.vue";
  import CreditsComponent from "@/components/CreditsComponent.vue";

  export default {
    name: "Home",
    components: {
      IncorrectComponent,
      CreditsComponent,
    },
    mounted() {
      this.getData();
    },
    data() {
      return {
        datos: [],
        capitales: [],
        countrys: [],
        flags: [],
        answerCorrect: "",
        alternativas: [],
        alternativasDesordenadas: [],
        hayData: false,
        check: false,
        bandCheck: 0,
        capital: "",
        correct: false,
        indexRptaCorrect: -1,
        numCorrects: 0,
        showComponentIncorrect: false,
        questions: ["es la capital de", "es la bandera del país de:"],
        question: [],
        indexQuestion: -1,
        urlBan: "",
        enumerationAlt: ["A", "B", "C", "D"],
      };
    },
    watch: {
      answerCorrect: function() {
        return this.countrys.filter((item) => item != this.answerCorrect);
      },
      check: function(value) {
        this.$nextTick(() => {
          let items = document.querySelectorAll(".alternative");
          if (value == true) {
            items.forEach((item) => {
              return item.classList.remove("hover");
            });
          } else {
            items.forEach((item) => {
              return item.classList.add("hover");
            });
          }
        });
      },
    },
    methods: {
      tryAgain(value) {
        if (value == true) {
          this.numCorrects = 0;
          this.showComponentIncorrect = false;
          this.continuePlay();
        }
      },
      getBand(value) {
        const options = {
          method: "GET",
          url: `https://restcountries.eu/rest/v2/capital/${value.replace(
            /['"]+/g,
            ""
          )}`,
        };

        axios
          .request(options)
          .then((response) => {
            let data = response.data;

            this.urlBan = data[0].flag;

            console.log("IMG:", this.urlBan);
          })
          .catch(function(error) {
            console.error(error);
          });
      },
      getData() {
        const options = {
          method: "GET",
          url: "https://restcountries.eu/rest/v2/all",
        };

        axios
          .request(options)
          .then((response) => {
            let data = response.data;

            data.forEach((item) => {
              let nuevo = {
                country: item.name,
                capital: item.capital,
                flag: item.flag,
              };
              this.capitales.push(nuevo.capital);
              this.countrys.push(nuevo.country);
              this.flags.push(nuevo.flag);
              this.datos.push(nuevo);
            });
            this.aleatory();
          })
          .catch(function(error) {
            console.error(error);
          });
      },
      questionAleatory() {
        const aleatory = Math.floor(Math.random() * this.questions.length);
        this.indexQuestion = aleatory;
        this.question = this.questions[aleatory];
      },
      aleatory() {
        // Se elige de manera aleatoria la pregunta
        this.questionAleatory();

        switch (this.indexQuestion) {
          case 0:
            // (1) index de la pregunta es 0 => Capitales de manera aleatoria
            this.aleatoryQuestionOne();
            break;
          case 1:
            // (2) index de la pregunta es 1 => Banderas de manera aleatoria
            this.aleatoryQuestionTwo();
            break;

          default:
            break;
        }
      },
      aleatoryQuestionOne() {
        // Se selecciona una capital de manera aleatoria
        const aleatory = Math.floor(Math.random() * this.capitales.length);
        this.capital = this.capitales[aleatory];

        this.hayData = true;

        // Se busca la respuesta
        let pais = this.datos.filter((item) => item.capital == this.capital);

        // Se guarda la respuesta
        this.answerCorrect = pais[0].country;

        // Agregar la respuesta al listado de alternativas.
        this.alternativas.push(pais[0].country);

        // Se busca 3 alternativas aleatorias
        for (let index = 0; index < 3; index++) {
          let numAleat = Math.floor(Math.random() * this.countrys.length);
          let alternative = this.countrys[numAleat];

          // Se agregan al listado de alternativas
          this.alternativas.push(alternative);
        }

        // Se desordena las alternativas
        this.alternativasDesordenadas = this.alternativas.sort(function() {
          return Math.random() - 0.5;
        });
      },
      aleatoryQuestionTwo() {
        // Se selecciona una bandera de manera aleatoria
        const aleatory = Math.floor(Math.random() * this.flags.length);
        this.urlBan = this.flags[aleatory];

        this.hayData = true;

        // Se busca la respuesta
        let pais = this.datos.filter((item) => item.flag == this.urlBan);

        // Se guarda la respuesta
        this.answerCorrect = pais[0].country;

        // Agregar la respuesta al listado de alternativas.
        this.alternativas.push(pais[0].country);

        // Se busca 3 alternativas aleatorias
        for (let index = 0; index < 3; index++) {
          let numAleat = Math.floor(Math.random() * this.countrys.length);
          let alternative = this.countrys[numAleat];

          // Se agregan al listado de alternativas
          this.alternativas.push(alternative);
        }

        // Se desordena las alternativas
        this.alternativasDesordenadas = this.alternativas.sort(function() {
          return Math.random() - 0.5;
        });
      },
      evaluateAnswer(answer, index) {
        this.check = true;

        this.bandCheck += 1;

        if (this.bandCheck == 1) {
          // Identificar la opción seleccionada
          let item = document.getElementById(index);
          // console.log("Elemento seleccionado: ", item);

          // Verificar si es la respuesta correcta
          if (answer == this.answerCorrect) {
            this.correct = true;
            this.numCorrects += 1;
            item.classList.add("correct");

            let icon = document.createElement("div");
            icon.classList.add("icon");

            icon.innerHTML = `<span class="material-icons-outlined p-3 " style="color:#F2F2F2">
                            check_circle_outline
                          </span>`;

            item.appendChild(icon);
          } else {
            // Se marca la respuesta como incorrecta

            item.classList.add("incorrect");

            let icon = document.createElement("div");
            icon.classList.add("icon");

            icon.innerHTML = `<span class="material-icons-outlined p-3" style="color:#F2F2F2">
                            cancel
                          </span>`;
            item.appendChild(icon);

            // Encontrar el index de la rpta correcta
            this.alternativasDesordenadas.forEach((item, index) => {
              if (item == this.answerCorrect) {
                this.indexRptaCorrect = index;
              }
            });

            // Automaticamente de marca la respuesta correcta
            let itemCorrect = document.getElementById(this.indexRptaCorrect);

            itemCorrect.classList.add("correct");

            let newIcon = document.createElement("div");
            newIcon.classList.add("icon");

            newIcon.innerHTML = `<span class="material-icons-outlined p-3 " style="color:#F2F2F2">
                            check_circle_outline
                          </span>`;

            itemCorrect.appendChild(newIcon);

            setTimeout(() => {
              this.showComponentIncorrect = true;
            }, 5000);
          }
        }
      },
      clear() {
        this.$nextTick(() => {
          // Limpiar los nuevas alternativas
          let items = document.querySelectorAll(".alternative");

          items.forEach((item) => {
            item.classList.remove("correct", "incorrect");
            let existe = document.querySelector(".icon");
            if (existe) {
              let padre = existe.parentNode;
              padre.removeChild(existe);
            }
            return item;
          });
        });
      },
      continuePlay() {
        this.check = false;

        // Limpiar la anterior respuesta correcta
        this.clear();

        this.answerCorrect = "";
        // Limpiar el listado de alternativas
        this.alternativas = [];
        this.alternativasDesordenadas = [];

        // Reiniciar la band
        this.bandCheck = 0;
        this.indexRptaCorrect = -1;

        this.correct = false;

        // Llamar a la función que genera las preguntas
        this.aleatory();
      },
    },
  };
</script>

<style lang="scss">
  .spinner {
    min-height: 100vh;
    margin: auto;
    display: flex;
    align-items: center;
    justify-content: center;
    background-image: url(../../public/images/background.png);
  }
  .home {
    background-image: url(../../public/images/background.png);
    background-position: center;
    background-repeat: no-repeat;
    background-size: cover;
    width: 100vw;
    min-height: 100vh;
  }
  .box {
    h1 {
      color: #f2f2f2;
      font-size: 28px;
      line-height: 54px;
    }
    figure {
      top: 0px;
      z-index: 999999999;
    }
    .card {
      border-radius: 24px;
      .question {
        color: #2f527b;
        font-size: 18px;
        font-weight: bold;
      }
      ol {
        list-style: inherit;
        li {
          list-style: inherit;
          background: #ffffff;
          border: 2px solid rgba(96, 102, 208, 0.7);
          box-sizing: border-box;
          border-radius: 12px;
          transition: 0.5s all;
          cursor: pointer;
          .wrapper {
            color: #6066d0;
            opacity: 0.8;
          }

          &.correct {
            background: #60bf88;
            border: 2px solid #60bf88;
            .wrapper {
              color: #fff;
            }
          }
          &.incorrect {
            background: #ea8282;
            border: 2px solid #ea8282;
            .wrapper {
              color: #fff;
            }
          }
        }
        li.hover:hover {
          background: #f9a826;
          border: 2px solid #f9a826;
          .wrapper {
            color: #fff;
          }
        }
      }
      .btn {
        background: #f9a826;
        border-radius: 12px;
        box-shadow: 0px 2px 4px rgba(252, 168, 47, 0.4);
        color: #fff;
        font-size: 18px;
        font-weight: 500;
        transition: 0.5s all;
      }
      .btn:hover {
        opacity: 0.9;
      }
    }
  }
  .img-band {
    width: 110px !important;
    border-radius: 4px;
    border: 1px solid #eee;
  }
  @media only screen and (max-width: 768px) {
    .p-16 {
      padding-left: 15px !important;
      padding-right: 15px !important;
    }
    .box h1 {
      font-size: 22px;
      line-height: 50px;
    }
    .box {
      width: 100% !important;
    }
    .box .wrapper {
      padding-left: 15px;
      padding-right: 15px;
    }
  }
  @media only screen and (max-width: 640px) {
    .img-correc {
      width: 110px !important;
    }
  }
</style>
