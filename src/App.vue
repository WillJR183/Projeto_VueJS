<template>
  <div id="app">

    <nav>
      <div class="nav-wrapper green darken-3">
        <a href="./" class="brand-logo center">Front-end -> Consumindo API Rest</a>
      </div>
    </nav>

    <div class="container">

      <ul>
        <li v-for="(erro, index) of errors" :key="index">
          campo <b>{{erro.field}}</b> - {{erro.defaultMessage}}
        </li>
      </ul>


      <form @submit.prevent="salvar">
        
        <label>Descrição</label>
        <input type="text" placeholder="Nome do Produto" v-model="produto.nome" >
        <label>Quantidade</label>
        <input type="number" placeholder="Quantidade de itens" v-model="produto.quantidade" >
        <label>Valor</label>
        <input type="number" placeholder="Valor Unitário" v-model="produto.valor" >

        <button class="waves-effect waves-light btn-small">Salvar<i class="material-icons left">save</i></button>

      </form>

      <table class="striped">

        <thead>

          <tr>
            <th>NOME</th>
            <th>QUANTIDADE</th>
            <th>VALOR</th>
            <th>OPÇÕES</th>
          </tr>

        </thead>

        <tbody>

          <tr v-for="produto of produtos" :key="produto.id">

            <td>{{ produto.nome }}</td>
            <td>{{ produto.quantidade }}</td>
            <td>{{ produto.valor }}</td>
            <td>
              <button @click="editar(produto)" class="waves-effect btn-small blue darken-1"><i class="material-icons">create</i></button>
              <button @click="remover(produto)" class="waves-effect btn-small red darken-1"><i class="material-icons">delete_sweep</i></button>
            </td>

          </tr>

        </tbody>
      
      </table>

    </div>

  </div>
</template>

<script>

import Produto from './services/produtos'

export default {
  name: 'app',
  data () {
    return {
      produto:{
        id: '',
        nome: '',
        quantidade: '',
        valor: ''
      },
      produtos: [],
      errors: []
    }
  },

  mounted(){
    this.listar()
  },

  methods:{
    
    listar(){
      Produto.listar().then(resposta => {
        this.produtos = resposta.data
      }).catch(e => {
        console.log(e)
      })
    },

    salvar(){
      if(!this.produto.id){
        Produto.salvar(this.produto).then(resposta => {
          this.produto = {}
          this.produto = resposta
          alert('Cadastrado com sucesso!')
          this.listar()
          this.errors = {}
        }).catch(e => {
          this.errors = e.response.data.errors
        })
      }else{
        Produto.atualizar(this.produto).then(resposta => {
          this.produto = {}
          this.errors = {}
          this.produto = resposta
          alert('Atualizado com sucesso!')
          this.listar()
        }).catch(e => {
          this.errors = e.response.data.errors
        })
      }
      
    },

    editar(produto){
      this.produto = produto
    },

    remover(produto){
      if(confirm('Deseja excluir o produto?')){
        Produto.apagar(produto).then(resposta => {
          this.listar()
          this.produto = resposta
          this.errors = {}
        }).catch(e => {
          this.errors = e.response.data.errors
        })
      }
    }
  }
}

</script>

<style>

</style>