<template>
  <div class="container">
    <div class="header-actions">
      <h1>🎸 MENU DE BEBIDAS</h1>
      <button @click="novaBebida" class="btn-primary">NOVA BEBIDA ⚡</button>
    </div>

    <!-- Estado de Loading -->
    <div v-if="loading" class="loading-container">
      <div class="loading-spinner"></div>
      <p>Carregando bebidas...</p>
    </div>

    <!-- Tabela de Bebidas -->
    <table v-else class="tabela-bebidas">
      <thead>
        <tr>
          <th>ID</th>
          <th>NOME</th>
          <th>PREÇO</th>
          <th>TIPO</th>
          <th>AÇÕES</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="bebida in bebidas" :key="bebida.id">
          <td>{{ bebida.id }}</td>
          <td>{{ bebida.nome }}</td>
          <td>R$ {{ bebida.preco }}</td>
          <td>{{ bebida.tipo }}</td>
          <td class="acoes">
            <button @click="editarBebida(bebida.id)" class="btn-edit">EDITAR</button>
            <button @click="confirmarExclusao(bebida)" class="btn-delete">APAGAR</button>
          </td>
        </tr>
        <tr v-if="bebidas.length === 0">
          <td colspan="5" class="empty-message">Nenhuma bebida cadastrada. Que tal adicionar uma?</td>
        </tr>
      </tbody>
    </table>

    <!-- Modal de Confirmação -->
    <ModalConfirmacao
      v-if="mostrarModal"
      titulo="Confirmar Exclusão"
      :mensagem="`Tem certeza que deseja APAGAR '${bebidaParaExcluir?.nome}' do menu?`"
      @confirmar="deletarBebida"
      @cancelar="cancelarExclusao"
    />

    <!-- Notificação -->
    <Notificacao
      :mostrar="notificacao.mostrar"
      :tipo="notificacao.tipo"
      :mensagem="notificacao.mensagem"
      @fechar="notificacao.mostrar = false"
    />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { useRouter } from 'vue-router';
import api from '../services/api';
import ModalConfirmacao from '../components/ModalConfirmacao.vue';
import Notificacao from '../components/Notificacao.vue';

const router = useRouter();
const bebidas = ref([]);
const loading = ref(false);
const mostrarModal = ref(false);
const bebidaParaExcluir = ref(null);
const notificacao = ref({
  mostrar: false,
  tipo: 'sucesso',
  mensagem: ''
});

const carregarBebidas = async () => {
  loading.value = true;
  try {
    const res = await api.get('/bebidas');

    const parser = new DOMParser();
    const xmlDoc = parser.parseFromString(res.data, "application/xml");

    let nodes = xmlDoc.getElementsByTagName("item");
    if(nodes.length === 0) nodes = xmlDoc.getElementsByTagName("Bebida");

    const lista = [];
    for (let i = 0; i < nodes.length; i++) {
      lista.push({
        id: nodes[i].getElementsByTagName("id")[0]?.textContent,
        nome: nodes[i].getElementsByTagName("nome")[0]?.textContent,
        preco: parseFloat(nodes[i].getElementsByTagName("preco")[0]?.textContent).toFixed(2),
        tipo: nodes[i].getElementsByTagName("tipo")[0]?.textContent,
      });
    }
    bebidas.value = lista;
  } catch (error) {
    console.error("Erro ao carregar bebidas", error);
    mostrarNotificacao('erro', 'Erro ao carregar bebidas');
  } finally {
    loading.value = false;
  }
};

const novaBebida = () => router.push('/bebidas/nova');
const editarBebida = (id) => router.push(`/bebidas/editar/${id}`);

const confirmarExclusao = (bebida) => {
  bebidaParaExcluir.value = bebida;
  mostrarModal.value = true;
};

const cancelarExclusao = () => {
  bebidaParaExcluir.value = null;
  mostrarModal.value = false;
};

const deletarBebida = async () => {
  try {
    await api.delete(`/bebidas/${bebidaParaExcluir.value.id}`);
    mostrarNotificacao('sucesso', 'Bebida apagada com sucesso!');
    carregarBebidas();
    mostrarModal.value = false;
    bebidaParaExcluir.value = null;
  } catch (error) {
    mostrarNotificacao('erro', 'Erro ao excluir bebida');
  }
};

const mostrarNotificacao = (tipo, mensagem) => {
  notificacao.value = { mostrar: true, tipo, mensagem };
  setTimeout(() => {
    notificacao.value.mostrar = false;
  }, 3000);
};

onMounted(() => {
  carregarBebidas();
});
</script>

<style scoped>
.container {
  max-width: 1000px;
  margin: 40px auto;
  background: #111;
  padding: 30px;
  border-top: 4px solid #e74c3c;
  box-shadow: 0 10px 50px rgba(0,0,0,0.8);
  border-radius: 4px;
  color: #eee;
}

.header-actions {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 30px;
}

h1 {
  color: #fff;
  font-family: 'Saira Extra Condensed', sans-serif;
  margin: 0;
  letter-spacing: 2px;
  font-size: 2rem;
}

.btn-primary {
  background: #2ecc71;
  color: #000;
  border: none;
  padding: 12px 24px;
  font-weight: bold;
  cursor: pointer;
  border-radius: 4px;
  text-transform: uppercase;
  transition: background 0.2s;
}

.btn-primary:hover {
  background: #27ae60;
}

.loading-container {
  text-align: center;
  padding: 60px 20px;
  color: #e74c3c;
}

.loading-spinner {
  width: 50px;
  height: 50px;
  border: 4px solid #222;
  border-top: 4px solid #e74c3c;
  border-radius: 50%;
  margin: 0 auto 20px;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

.tabela-bebidas {
  width: 100%;
  border-collapse: collapse;
  text-align: left;
}

.tabela-bebidas th {
  background: #222;
  color: #e74c3c;
  padding: 14px;
  border-bottom: 2px solid #333;
  font-weight: bold;
  text-transform: uppercase;
  font-size: 0.9rem;
}

.tabela-bebidas td {
  padding: 14px;
  border-bottom: 1px solid #222;
  color: #eee;
}

.tabela-bebidas tbody tr:hover {
  background: #1a1a1a;
}

.acoes {
  display: flex;
  gap: 10px;
}

.btn-edit,
.btn-delete {
  border: none;
  padding: 8px 16px;
  cursor: pointer;
  border-radius: 4px;
  font-weight: bold;
  font-size: 0.85rem;
  text-transform: uppercase;
  transition: background 0.2s;
}

.btn-edit {
  background: #f39c12;
  color: #fff;
}

.btn-edit:hover {
  background: #e67e22;
}

.btn-delete {
  background: #e74c3c;
  color: #fff;
}

.btn-delete:hover {
  background: #c0392b;
}

.empty-message {
  text-align: center;
  color: #888;
  font-style: italic;
}
</style>