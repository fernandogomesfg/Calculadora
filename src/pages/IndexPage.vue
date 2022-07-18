<template>
  <q-page padding>
    <div class="row justify-center">
      <div class="col-12 col-md-8">
        <q-card>
          <q-card-section class="bg-primary text-white">
            <div class="text-h6">
              Calculadora
            </div>
          </q-card-section>

          <q-card-section>
            <div class="text-h5 text-grey-5 text-right">
              {{acumulador + actual}}
            </div>

            <div class="text-h4 text-right">
              {{resultado}}
            </div>
          </q-card-section>

          <q-card-section class="bg-grey-4">
            <div class="row q-col-gutter-sm">
              <div
                class="col-3"
                v-for="(btn, index) in botoes" :key="index"
              >
                <q-btn
                  class="full-width text-h6"
                  :color="naoNumero(btn) ? 'indigo' : 'grey-2'"
                  :text-color="naoNumero(btn) ? 'white' : 'grey-8'"
                  @click="btnAction(btn)"
                  >
                  {{btn}}
                </q-btn>
              </div>

              <div class="col-6">
                <q-btn
                  class="full-width text-h6"
                  color="indigo"
                  @click="btnReset"
                  >
                  Reset
                </q-btn>
              </div>
              <div class="col-6">
                <q-btn
                  class="full-width text-h6"
                  color="orange"
                  @click="btnResultado"
                  >
                  =
                </q-btn>
              </div>
            </div>


          </q-card-section>
        </q-card>
        <pre>
          {{arrayHistorico}}
        </pre>
      </div>
    </div>
  </q-page>
</template>

<script>
import {ref} from 'vue'
import {
  evaluate, round
} from 'mathjs'
export default {
  setup() {
    const botoes = [7, 8, 9, "%", 4, 5, 6, "+", 1, 2, 3, "-", ".", 0, "/", "*"];

    const naoNumero = valor => isNaN(valor)

    const actual = ref('')
    const acumulador = ref('')
    const resultado = ref('')
    const operadorClick = ref(true)
    const arrayHistorico = ref([])

    const btnAction = valor =>{
      if(!naoNumero (valor)){
        if(operadorClick.value) {
          actual.value = ""
          operadorClick.value = false
        }

        actual.value = `${actual.value}${valor}`
      }else{
        executarOperacao(valor)
      }
    }

    const executarOperacao = valor =>{
      if (valor === ".") {
        if(actual.value.indexOf('.') === -1) {
          actual.value = `${actual.value}${valor}`
        }

        return
      }

      if (valor === "%") {
        if(actual.value !== ''){
          actual.value = `${parseFloat(actual.value) / 100}`
        }

        return
      }

      outrasOperacoes(valor)
    }

    const outrasOperacoes = valor => {
      if(!operadorClick.value){
        acumulador.value += `${actual.value} ${valor} `
        actual.value = ""
        operadorClick.value = true
      }

    }


    const btnReset = () => {
      actual.value = ""
      acumulador.value = ""
      resultado.value = ""
      operadorClick.value = true
    }

    const btnResultado = () =>{
      if (!operadorClick.value) {
         resultado.value = evaluate(acumulador.value + actual.value)
         arrayHistorico.value.push(`${acumulador.value} + ${actual.value} = ${resultado.value}`)
      } else {
        resultado.value = "Erro"
      }

    }


    return{
      botoes,
      naoNumero,
      btnAction,
      actual,
      executarOperacao,
      acumulador,
      btnReset,
      btnResultado,
      resultado,
      arrayHistorico
    }

  },
}

</script>

<style scoped>
  .text-h5{
    height: 23px;
  }

  .text-h4{
    height: 50px;
  }
</style>
