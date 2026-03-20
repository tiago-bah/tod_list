<script setup>
import { ref } from 'vue'

const tarefas = ref([
  'seminario geografia',
  'seminario portugues',
  'avaliação matematica',
  'avaliação fisica',
  'folder sociologia',
])
const novoItem = ref('')
const alteracao = ref('-1')
const buscar = ref('')
const concluidas = ref([])

function add() {
  if (novoItem.value.trim().length < 5) {
    aviso.value = true
    return
  } else {
    if (alteracao.value == -1) {
      tarefas.value.push(novoItem.value)
      novoItem.value = ''
    } else {
      tarefas.value.splice(alteracao.value, 1, novoItem.value)
      novoItem.value = ''
      alteracao.value = -1
    }
  }
}

function concluir(item) {
  const posicao = concluidas.value.indexOf(item)
  if (posicao === -1) {
    concluidas.value.push(item)
  } else {
    concluidas.value.splice(posicao, 1)
  }
}
const aviso = ref(false)

function deleteTarefa(item) {
  const posicao = tarefas.value.indexOf(item)
  tarefas.value.splice(posicao, 1)
}
function editarTarefa(item) {
  alteracao.value = tarefas.value.indexOf(item)
  novoItem.value = item
}
</script>
<template>
  <div class="container">
    <h1>Lista de tarefas</h1>

    <input
      type="text"
      v-model="novoItem"
      @keyup.enter="add(novoItem)"
      @input="novoItem.length < 5 ? (aviso = true) : (aviso = false)"
    />
    <button @click="add(novoItem)">add</button>
    <div v-show="aviso" class="aviso">Digite ao menos 5 caracteres!</div>
    <ul>
      <li
        v-for="tarefa in tarefas"
        :key="tarefa"
        v-show="tarefa.toLowerCase().includes(buscar.toLowerCase())"
      >
        <span
          @click="concluir(tarefa)"
          :class="{ feito: concluidas.includes(tarefa) }"
          style="cursor: pointer"
        >
          {{ tarefa }}
        </span>
        <span>
          <a href="#" @click.prevent="editarTarefa(tarefa)">Editar</a>
          <a href="#" @click.prevent="deleteTarefa(tarefa)" class="Delete">Delete</a>
        </span>
      </li>
    </ul>
    <p>Concluídas: {{ concluidas.length }} - Pendentes: {{ tarefas.length - concluidas.length }}</p>
    <div>
      <input type="text" v-model="buscar" placeholder="Buscar..." />
    </div>
  </div>
</template>
<style scoped>
.feito {
  text-decoration: line-through;
  color: gray;
}
.Delete{
  color: rgb(255, 0, 0); 
}


li{
  padding: 0 0 7px;
}
input{
  padding: 9px;
  border-radius: 20px;
}
</style>
