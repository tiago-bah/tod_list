<script setup>
import { ref, computed } from 'vue'

const tarefas = ref([
  { id: 1, desc: 'fazer o breakdance', status: 'pendente' },
  { id: 2, desc: 'ler livros', status: 'pendente' },
  { id: 3, desc: 'estudar o neymar', status: 'concluida' },
])

const texto = ref('')
const filtro = ref('')
const editandoId = ref(0)

const gerarID = () => {
  return Math.max(...tarefas.value.map(item => item.id), 0) + 1
}

const acharPosicao = (id) => {
  return tarefas.value.findIndex(item => item.id == id)
}

const salvar = () => {
  if (texto.value.trim() === '') {
    alert('Digite algo')
    return
  }

  if (editandoId.value !== 0) {
    const posicao = acharPosicao(editandoId.value)

    if (posicao !== -1) {
      tarefas.value[posicao].desc = texto.value
    }

    editandoId.value = 0
  } else {
    tarefas.value.push({
      id: gerarID(),
      desc: texto.value,
      status: 'pendente'
    })
  }

  texto.value = ''
}

const editar = (id) => {
  const posicao = acharPosicao(id)
  if (posicao === -1) return

  texto.value = tarefas.value[posicao].desc
  editandoId.value = id
}

const remover = (id) => {
  const posicao = acharPosicao(id)

  if (posicao !== -1) {
    tarefas.value.splice(posicao, 1)
  }
}

const concluir = (id) => {
  const posicao = acharPosicao(id)
  if (posicao === -1) return

  if (tarefas.value[posicao].status === 'pendente') {
    tarefas.value[posicao].status = 'concluida'
  } else {
    tarefas.value[posicao].status = 'pendente'
  }
}

const tarefasFiltradas = computed(() => {
  if (filtro.value === '') return tarefas.value

  return tarefas.value.filter(item =>
    item.desc.toLowerCase().includes(filtro.value.toLowerCase())
  )
})

const progresso = computed(() => {
  let concluidas = 0
  let pendentes = 0

  tarefas.value.forEach(item => {
    if (item.status === 'pendente') {
      pendentes++
    } else {
      concluidas++
    }
  })

  return 'Concluidas: ' + concluidas + ' | Pendentes: ' + pendentes
})
</script>

<template>
  <div class="container">
    <h2>{{ progresso }}</h2>

    <input v-model="texto" placeholder="Digite uma tarefa" @keyup.enter="salvar" />
    <button @click="salvar">
      {{ editandoId !== 0 ? 'Salvar' : 'Adicionar' }}
    </button>

    <button v-if="editandoId !== 0" @click="editandoId = 0; texto = ''">
      Cancelar
    </button>

    <ul>
      <li v-for="item in tarefasFiltradas" :key="item.id">
        <span
          class="tarefa"
          :class="{ concluida: item.status === 'concluida' }"
          @click="concluir(item.id)"
        >
          {{ item.desc }} ({{ item.status }})
        </span>

        <div>
          <button @click="editar(item.id)">Editar</button>
          <button @click="remover(item.id)">Remover</button>
        </div>
      </li>
    </ul>

    <input v-model="filtro" placeholder="Filtrar" />
  </div>
</template>

<style>
.container {
  text-align: center;
  margin-top: 40px;
  color: black;
}

ul {
  list-style: none;
  padding: 0;
  width: 300px;
  margin: 20px auto;
}

li {
  margin-bottom: 10px;
}

.tarefa {
  cursor: pointer;
}
.container{
  background-color: cadetblue;
  width: 90%;
  height: 90%;
}
h2{
  font-weight: bold;
}

</style>