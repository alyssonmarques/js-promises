<script>
  export default {

    data () {
      return {
        msg: '',
        fila: [1000, 1200, 2000, 5000, 1400, 3000, 2500, 500, 7000, 2000],
      }
    },

    methods: {
      log(l) {
        this.msg = this.msg + '<br>' + l
      },
      limparLog() {
        this.msg = ''
      },
      newPromise(timeout) {
        return new Promise((resolve, reject) => {
          this.log('criou ' + timeout)
          setTimeout(() => {
            this.log('resolveu ' + timeout)
            resolve(timeout, timeout)
          }, timeout)
        })
      },
      serial() {
        this.limparLog()
        this.newPromise(3000)
          .then(() => this.newPromise(2000))
          .then(() => this.newPromise(5000))
      },
      paralelo() {
        this.limparLog()
        this.newPromise(3000)
        this.newPromise(2000)
        this.newPromise(5000)
      },
      paraleloAguardandoTodas() {
        this.limparLog()
        this.paraleloPromiseAll().then(v => this.log(v))
      },

      paraleloPromiseAll() {
        this.limparLog()
        return Promise.all([
          this.newPromise(3000),
          this.newPromise(2000),
          this.newPromise(5000)
        ])
      },

      processarProximoDaFila() {
        return new Promise((resolve, reject) => {
          if (this.fila.length == 0) {
            reject('Fila está vazia')
            return
          }
          const elemento = this.fila.shift()
          this.log('processando ' + elemento)
          setTimeout(() => {
            resolve(elemento)
          }, elemento)
        })
      },

      processarFila(quantidadeElementos) {
        for (let i = 0; i < quantidadeElementos; i++) {
          this.processarProximoDaFila()
            .then(r => this.processarFila(1))
            .catch(e => this.log(e))
        }
      }

    },
    mounted() {
    }
  }
</script>

<template>
  <div id="app">
    <header>
      <div class="wrapper">
        Promises
      </div>
    </header>
    
    <main>
      <div>Fila: {{ fila }}</div>
      <div>
        <button @click="serial">Serial</button>
        <button @click="paralelo">Paralelo</button>
        <button @click="paraleloAguardandoTodas">Paralelo aguardando todas</button>
        <button @click="processarFila(3)">Processar Fila</button>
      </div>
      <span v-html="msg">
      </span>
    </main>
  </div>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
