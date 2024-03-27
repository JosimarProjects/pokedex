<template>
  <div class="container">
    <div class="row mt-2">

      <!-- início lado esquerdo -->
      <div class="col mb-2">
        <div class="card palco">
          <div class="card-header"></div>

          <div class="card-body bg-pokebola bg-normal">
            <div class="pokemon">
              <!--  <transition name="pulo" :duration="{enter:2000, leave:1000}">
                  <img src="../assets/imgs/pokemons/001.png" v-if="exibir">
                </transition>
                -->
              <transition

                  @before-enter="antesDaEntrada"
                  @enter="duranteAEntrada"
                  @after-enter="exibirEvolucoesTransicao"
                  @before-leave="ocutarEvolucoesTransicao"

                  enter-active-class="animate__animated animate__bounceIn"
                  leave-active-class="animate__animated animate__backOutUp"
              >
                <img :src="require(`@/assets/imgs/pokemons/${pokemon.imagem}`)" v-if="exibir">
              </transition>

              <div class="evolucoes">
                <transition name="fade" v-for="e in pokemon.evolucoes" :key="e">
                  <img :src="require(`../assets/imgs/pokemons/${e.toString().padStart(3, '0')}.png`)"
                       v-if="exibirEvolucoes">

                  <!-- <img src="../assets/imgs/pokemons/003.png" v-if="exibirEvolucoes">-->
                </transition>

              </div>
            </div>
          </div>

          <div class="card-footer">

            <nav class="nav nav-pills nav-fill">
              <router-link
                  class="nav-link nav-item text-white"
                  :to="{path: '/sobre' }"
                  exact-active-class="active"
              >
                Sobre
              </router-link>
              <router-link class="nav-link nav-item text-white" :to="{path: '/status' }" exact-active-class="active">
                status
              </router-link>
              <router-link class="nav-link nav-item text-white" :to="{path: '/habilidades' }"
                           exact-active-class="active">habilidades
              </router-link>

            </nav>

            <div class="detalhes">
              <router-view v-slot="{ Component }"
                           :pokemon="pokemon"
                           @adicionarHabilidade="adicionarHabilidade"
                           @removerHabilidade="removerHabilidade"
              >
                <transition
                    enter-active-class="animate__animated animate__zoomInDown"
                >
                  <component :is="Component"/>
                </transition>
              </router-view>
            </div>

          </div>
        </div>
      </div>
      <!-- fim lado esquerdo -->

      <!-- início lado direito -->
      <div class="col mb-2 pokedex">
        <div class="row">
          <div class="col">
            <h1>Pokédex</h1>
          </div>
        </div>

        <div class="row">
          <div class="col">
            <select class="form-select" v-model="ordenacao">
              <option disabled>Ordenar por</option>
              <option value="1">Id crescente</option>
              <option value="2">Id decrescrente</option>
              <option value="3">De A - Z</option>
              <option value="4">De z - A (localeCompare)</option>

            </select>
          </div>

          <div class="col">
            <input
                type="text"
                class="form-control"
                placeholder="Pesquisar pokémon"
                v-model="nomePokemon"
                @keyup="filtrarPokemonsPorNome"
            >
          </div>
        </div>

        <div class="row">
          <div class="pokedex-catalogo">

            <!-- início listagem dinâmica -->
            <transition-group name="ordenacao">
            <div
                v-for="p in pokemons" :key="p.id"
                 :class="`cartao-pokemon bg-${p.tipo}`"
                 @click="analisarPokemon(p)">
              <h1>{{ p.id }} {{ p.nome }}</h1>
              <span>{{ p.tipo }}</span>
              <div class="cartao-pokemon-img">
                <transition
                    appear
                    enter-active-class="animate__animated animate__fadeInDown"
                >
                  <img :src="require(`../assets/imgs/pokemons/${p.imagem}`)">
                </transition>
              </div>
            </div>
            </transition-group>
            <!-- fim listagem dinâmica -->

          </div>
        </div>
      </div>
      <!-- fim lado direito -->

    </div>
  </div>
</template>

<script>
export default {
  name: 'Home',
  data: () => ({
    exibir: false,
    exibirEvolucoes: false,
    nomePokemon: '',

   /* pokemons: [
      {id: 1, nome: 'Bulbasaur', tipo: 'grama', imagem: '001.png', evolucoes: [2, 3]},
      {id: 2, nome: 'Ivysaur', tipo: 'grama', imagem: '002.png', evolucoes: [3]},
      {id: 3, nome: 'Venusaur', tipo: 'grama', imagem: '003.png', evolucoes: []},
      {id: 4, nome: 'Charmander', tipo: 'fogo', imagem: '004.png', evolucoes: [5, 6]},
      {id: 5, nome: 'Charmeleon', tipo: 'fogo', imagem: '005.png', evolucoes: [6]},
      {id: 6, nome: 'Charizard', tipo: 'fogo', imagem: '006.png', evolucoes: []},
      {id: 7, nome: 'Squirtle', tipo: 'agua', imagem: '007.png', evolucoes: [8, 9]},
      {id: 8, nome: 'Wartortle', tipo: 'agua', imagem: '008.png', evolucoes: [9]},
      {id: 9, nome: 'Blastoise', tipo: 'agua', imagem: '009.png', evolucoes: []},
      {id: 10, nome: 'Caterpie', tipo: 'inseto', imagem: '010.png', evolucoes: [11, 12]},
      {id: 11, nome: 'Metapod', tipo: 'inseto', imagem: '011.png', evolucoes: [12]},
      {id: 12, nome: 'Butterfree', tipo: 'inseto', imagem: '012.png', evolucoes: []},
      {id: 13, nome: 'Weedle', tipo: 'inseto', imagem: '013.png', evolucoes: [14, 15]},
      {id: 14, nome: 'Kakuna', tipo: 'inseto', imagem: '014.png', evolucoes: [15]},
      {id: 15, nome: 'Beedrill', tipo: 'inseto', imagem: '015.png', evolucoes: []},
      {id: 16, nome: 'Pidgey', tipo: 'normal', imagem: '016.png', evolucoes: [17, 18]},
      {id: 17, nome: 'Pidgeotto', tipo: 'normal', imagem: '017.png', evolucoes: [18]},
      {id: 18, nome: 'Pidgeot', tipo: 'normal', imagem: '018.png', evolucoes: []}
    ]*/
    pokemons: [],
    pokemon: {},
    ordenacao: ''
  }),
  watch: {
    nomePokemon(valorNovo) {
      this.pokemonsList()

      this.filtrarPokemonsPorNome(valorNovo)
    },
    ordenacao(valorNovo) {
      console.log('ordenacao', valorNovo)
      if (valorNovo === '1') { //ordernação por id crescente
        this.pokemons.sort((proximo, atual) => {
          if (atual.id > proximo.id) {
            return -1
          }
          if (atual.id < proximo.id) {
            return 1
          }
          return 0

        })
      }
      if (valorNovo === '2') { //ordernação por id decrescente
        //return 1 para indicar que o primeiro elemento é maior
        //return -1 para indicar que o segundo elemento é maior
        //return 0 para indicar que os elementos são iguais

        this.pokemons.sort((proximo, atual) => {
          if (atual.id > proximo.id) {
            return 1
          }
          if (atual.id < proximo.id) {
            return -1
          }
          return 0

        })
      }
      if (valorNovo == 3) {
        this.pokemons.sort((proximo, atual) => {
          if (atual.nome < proximo.nome) {
            return 1
          }
          if (atual.nome > proximo.nome) {
            return -1
          }
          return 0

        })

      }
      if (valorNovo == 4) {
        this.pokemons.sort((proximo, atual) => {// ordernação alfabetica de z a A
          return atual.nome.localeCompare(proximo.nome)
        })
      }

    }

  },
  created() {
    this.pokemonsList()

  },
  /*created() {
    fetch('http://localhost:3000/pokemons')
        .then(response => {
          return response.json()
        })
        .then(data => {
          this.pokemons = data
        })

  },*/


  methods: {
    pokemonsList() {
      this.pokemons = [
        {
          "id": 1,
          "nome": "Bulbasaur",
          "tipo": "grama",
          "imagem": "001.png",
          "evolucoes": [2,3],
          "habilidades": ["Ataque fúria", "Esquivar"],
          "status": {
            "forca": 30,
            "agilidade": 40,
            "ataque": 20,
            "defesa": 25
          }
        },
        {
          "id": 2,
          "nome": "Ivysaur",
          "tipo": "grama",
          "imagem": "002.png",
          "evolucoes": [3],
          "habilidades": ["Ataque fúria", "Esquivar"],
          "status": {
            "forca": 30,
            "agilidade": 40,
            "ataque": 20,
            "defesa": 25
          }
        },
        {
          "id": 3,
          "nome": "Venusaur",
          "tipo": "grama",
          "imagem": "003.png",
          "evolucoes": [],
          "habilidades": ["Ataque fúria", "Esquivar"],
          "status": {
            "forca": 30,
            "agilidade": 40,
            "ataque": 20,
            "defesa": 25
          }
        },
        {
          "id": 4,
          "nome": "Charmander",
          "tipo": "fogo",
          "imagem": "004.png",
          "evolucoes": [5, 6],
          "habilidades": ["Ataque fúria", "Esquivar"],
          "status": {
            "forca": 40,
            "agilidade": 40,
            "ataque": 20,
            "defesa": 25
          }
        },
        {
          "id": 5,
          "nome": "Charmeleon",
          "tipo": "fogo",
          "imagem": "005.png",
          "evolucoes": [6],
          "habilidades": ["Ataque fúria", "Esquivar"],
          "status": {
            "forca": 30,
            "agilidade": 40,
            "ataque": 20,
            "defesa": 25
          }
        },
        {
          "id": 6,
          "nome": "Charizard",
          "tipo": "fogo",
          "imagem": "006.png",
          "evolucoes": [],
          "habilidades": ["Ataque fúria", "Esquivar"],
          "status": {
            "forca": 30,
            "agilidade": 40,
            "ataque": 20,
            "defesa": 25
          }
        },
        {
          "id": 7,
          "nome": "Squirtle",
          "tipo": "agua",
          "imagem": "007.png",
          "evolucoes": [8,9],
          "habilidades": ["Jato de água", "mergulho", "cabeçada"],
          "status": {
            "forca": 30,
            "agilidade": 40,
            "ataque": 20,
            "defesa": 25
          }
        },
        {
          "id": 8,
          "nome": "Wartortle",
          "tipo": "agua",
          "imagem": "008.png",
          "evolucoes": [9],
          "habilidades": ["Ataque fúria", "Esquivar"],
          "status": {
            "forca": 30,
            "agilidade": 40,
            "ataque": 20,
            "defesa": 25
          }
        },
        {
          "id": 9,
          "nome": "Blastoise",
          "tipo": "agua",
          "imagem": "009.png",
          "evolucoes": [],
          "habilidades": ["Ataque fúria", "Esquivar"],
          "status": {
            "forca": 30,
            "agilidade": 40,
            "ataque": 20,
            "defesa": 25
          }
        },
        {
          "id": 10,
          "nome": "Caterpie",
          "tipo": "inseto",
          "imagem": "010.png",
          "evolucoes": [11,12],
          "habilidades": ["Ataque fúria", "Esquivar"],
          "status": {
            "forca": 30,
            "agilidade": 40,
            "ataque": 20,
            "defesa": 25
          }
        },
        {
          "id": 11,
          "nome": "Metapod",
          "tipo": "inseto",
          "imagem": "011.png",
          "evolucoes": [12],
          "habilidades": ["Ataque fúria", "Esquivar"],
          "status": {
            "forca": 30,
            "agilidade": 40,
            "ataque": 20,
            "defesa": 25
          }
        },
        {
          "id": 12,
          "nome": "Butterfree",
          "tipo": "inseto",
          "imagem": "012.png",
          "evolucoes": [],
          "habilidades": ["Ataque fúria", "Esquivar"],
          "status": {
            "forca": 30,
            "agilidade": 40,
            "ataque": 20,
            "defesa": 25
          }
        },
        {
          "id": 13,
          "nome": "Weedle",
          "tipo": "inseto",
          "imagem": "013.png",
          "evolucoes": [14,15],
          "habilidades": ["Ataque fúria", "Esquivar"],
          "status": {
            "forca": 30,
            "agilidade": 40,
            "ataque": 20,
            "defesa": 25
          }
        },
        {
          "id": 14,
          "nome": "Kakuna",
          "tipo": "inseto",
          "imagem": "014.png",
          "evolucoes": [15],
          "habilidades": ["Ataque fúria", "Esquivar"],
          "status": {
            "forca": 30,
            "agilidade": 40,
            "ataque": 20,
            "defesa": 25
          }
        },
        {
          "id": 15,
          "nome": "Beedrill",
          "tipo": "inseto",
          "imagem": "015.png",
          "evolucoes": [],
          "habilidades": ["Ataque fúria", "Esquivar"],
          "status": {
            "forca": 30,
            "agilidade": 40,
            "ataque": 20,
            "defesa": 25
          }
        },
        {
          "id": 16,
          "nome": "Pidgey",
          "tipo": "normal",
          "imagem": "016.png",
          "evolucoes": [17,18],
          "habilidades": ["Ataque fúria", "Esquivar"],
          "status": {
            "forca": 30,
            "agilidade": 40,
            "ataque": 20,
            "defesa": 25
          }
        },
        {
          "id": 17,
          "nome": "Pidgeotto",
          "tipo": "normal",
          "imagem": "017.png",
          "evolucoes": [18],
          "habilidades": ["Ataque fúria", "Esquivar"],
          "status": {
            "forca": 30,
            "agilidade": 40,
            "ataque": 20,
            "defesa": 25
          }
        },
        {
          "id": 18,
          "nome": "Pidgeot",
          "tipo": "normal",
          "imagem": "018.png",
          "evolucoes": [],
          "habilidades": ["Ataque fúria", "Esquivar"],
          "status": {
            "forca": 30,
            "agilidade": 40,
            "ataque": 20,
            "defesa": 25
          }
        }
      ]

    },

    exibirEvolucoesTransicao() {
      this.exibirEvolucoes = true;
    },
    ocutarEvolucoesTransicao() {
      this.exibirEvolucoes = false;
    },
    analisarPokemon(p) {

      let mudaPokemonAnalisado = false

      if (this.pokemon.id != p.id && this.exibir) {
        setTimeout(() => {
          this.analisarPokemon(p) // recursividade
        }, 1000);
        mudaPokemonAnalisado = true
      }
      this.pokemon = p;
      this.exibir = !this.exibir
      this.exibirEvolucoes = !this.exibirEvolucoes

      if (!this.exibir && !mudaPokemonAnalisado) {
        this.pokemon = {}
      }
    },
    adicionarHabilidade(habilidade) {
      if (!this.pokemon.habilidades) {
        this.pokemon.habilidades = []
      }
      this.pokemon.habilidades.push(habilidade)
    },
    removerHabilidade(indice) {
      if (this.pokemon.habilidades[indice]) {
        // console.log('remover habilidade', indice)
        this.pokemon.habilidades.splice(indice, 1)
      }
    },
   /* filtrarPokemonsPorNome() {
      fetch(`http://localhost:3000/pokemons?nome_like=${this.nomePokemon}`)
          .then(response => {
            return response.json()
          })
          .then(data => {
            this.pokemons = data
          })



  }*/
  filtrarPokemonsPorNome(valorNovo) {
    this.pokemons = this.pokemons.filter(p => {
      return p.nome.toLowerCase().includes(valorNovo.toLowerCase())
    })
  },


  }
}
</script>

<style>
body {
  background-color: #dee3eb;
}
</style>

<style scoped>

@import "~@/assets/css/animacoes.css";


.pokedex {
  padding: 20px;
  background-color: #ffffff;
  -webkit-box-shadow: 2px 2px 10px rgba(200, 200, 200, 0.77);
  -moz-box-shadow: 2px 2px 10px rgba(200, 200, 200, 0.77);
  box-shadow: 2px 2px 10px rgba(200, 200, 200, 0.77);
  border-radius: 10px;
}

.pokedex-catalogo {
  overflow: auto;
  display: flex;
  flex-wrap: wrap;
  height: 400px;
  width: 98%;
  margin-top: 10px;
}

.cartao-pokemon {
  position: relative;
  margin: 5px;
  width: 150px;
  height: 115px;
  cursor: pointer;
  border-radius: 5px;
  -webkit-box-shadow: 2px 2px 2px rgba(200, 200, 200, 0.77);
  -moz-box-shadow: 2px 2px 2px rgba(200, 200, 200, 0.77);
  box-shadow: 2px 2px 2px rgba(200, 200, 200, 0.77);
}

.cartao-pokemon h1 {
  color: #fff;
  font-size: 14px;
  margin: 5px 0px 0px 5px;
  padding: 0px;
}

.cartao-pokemon span {
  color: #fff;
  position: absolute;
  background: rgba(255, 255, 255, 0.3);
  font-size: 12px;
  margin: 10px 0px 0px 5px;
  padding: 5px 10px 5px 10px;
  border-radius: 25px;
}

.cartao-pokemon img {
  max-width: 60%;
  max-height: 60%;
  float: right;
}

.bg-grama {
  background-color: #2d8f78;
}

.bg-fogo {
  background-color: #e47373
}

.bg-agua {
  background-color: #5a9ed2
}

.bg-inseto {
  background-color: #26d3ab
}

.bg-normal {
  background-color: #cecece
}

.bg-pokebola {
  background-image: url("~@/assets/imgs/pokebola.png");
  background-repeat: no-repeat;
  background-position: bottom right;
}

.palco {
  color: #fff;
  background-color: #333;
  -webkit-box-shadow: 2px 2px 10px rgba(230, 223, 223, 0.77);
  -moz-box-shadow: 2px 2px 10px rgba(230, 223, 223, 0.77);
  box-shadow: 2px 2px 10px rgba(230, 223, 223, 0.77);
  border-radius: 10px;
}

.pokemon {
  display: block;
  text-align: center;
  height: 215px;
}

.detalhes {
  margin: 20px 30px 20px 30px;
}

.evolucoes {
  position: absolute;
  top: 0px;
  right: 0px;
  height: 70px;
}

.evolucoes img {
  cursor: pointer;
  max-width: 100%;
  max-height: 100%;
}


</style>
