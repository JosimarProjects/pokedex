<template>
    <div>
      <div v-if="!pokemon.habilidades">
        Selecione um Pokémon
      </div>

      <div v-else>
        <table class="table text-white">
            <tbody>
            <transition-group name="lista">
              <tr v-for="(h, indice) in habilidadesOrdenadas" :key="indice">
                <td>{{ h }}</td>
                    <td class="d-flex justify-content-end">
                      <button
                          type="button"
                          class="btn btn-danger btn-sm"
                          @click="$emit('removerHabilidade', indice)">x
                      </button>
                    </td>
                </tr>
            </transition-group>
            </tbody>
        </table>
        <input
            type="text"
            class="form-control"
            placeholder="Adicionar habilidade"
            v-model="habilidade"
            @keyup.enter="adicionarHabilidade"
        >
      </div>

    </div>
</template>

<style scoped>
.table td {
    border: none;
}
</style>
<script>
export default {
  name: 'Habilidades',
  props: {
    pokemon: {
      type: Object,
      required: true
    }
  },
  data: () => ({
    habilidade: 'teste'
  }),
  methods: {
    adicionarHabilidade() {
      this.$emit('adicionarHabilidade', this.habilidade)
      this.habilidade = ''
    }
  },
  computed: {
    habilidadesOrdenadas() {
      let habilidades = this.pokemon.habilidades
      return habilidades.sort()

    }
  },
}
</script>
