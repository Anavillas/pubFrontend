<template>
  <div class="container">
    <h1>{{ isEdicao ? '🎸 EDITAR BEBIDA' : '🎸 NOVA BEBIDA' }}</h1>
    <div class="divider"></div>

    <form @submit.prevent="salvarBebida">
      <div class="form-group">
        <label>NOME DA BEBIDA</label>
        <input 
          v-model="bebida.nome" 
          type="text" 
          placeholder="Ex: Chopp Artesanal" 
          required 
        />
      </div>

      <div class="form-group">
        <label>PREÇO (R$)</label>
        <input 
          v-model="bebida.preco" 
          type="number" 
          step="0.01" 
          placeholder="Ex: 15.50" 
          required 
        />
      </div>

      <div class="form-group">
        <label>TIPO</label>
        <input 
          v-model="bebida.tipo" 
          type="text" 
          placeholder="Ex: Cerveja, Drink, Não Alcoólico" 
          required 
        />
      </div>

      <div class="form-group">
        <label>DESCRIÇÃO</label>
        <textarea 
          v-model="bebida.descricao" 
          rows="4" 
          placeholder="Detalhes da bebida..." 
          required
        ></textarea>
      </div>

      <div class="actions">
        <button type="submit" class="btn-primary" :disabled="salvando">
          {{ salvando ? 'SALVANDO...' : 'SALVAR ⚡' }}
        </button>
        <button type="button" @click="voltar" class="btn-secondary">
          CANCELAR
        </button>
      </div>
    </form>

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
import { useRouter, useRoute } from 'vue-router';
import api from '../services/api';
import Notificacao from '../components/Notificacao.vue';

const router = useRouter();
const route = useRoute();

const isEdicao = ref(false);
const bebida = ref({ nome: '', preco: '', tipo: '', descricao: '' });
const salvando = ref(false);
const notificacao = ref({
  mostrar: false,
  tipo: 'sucesso',
  mensagem: ''
});

onMounted(async () => {
  if (route.params.id) {
    isEdicao.value = true;
    try {
      const res = await api.get(`/bebidas/${route.params.id}`);

      const parser = new DOMParser();
      const xmlDoc = parser.parseFromString(res.data, "application/xml");

      bebida.value = {
        nome: xmlDoc.getElementsByTagName("nome")[0]?.textContent || '',
        preco: parseFloat(xmlDoc.getElementsByTagName("preco")[0]?.textContent || 0).toFixed(2),
        tipo: xmlDoc.getElementsByTagName("tipo")[0]?.textContent || '',
        descricao: xmlDoc.getElementsByTagName("descricao")[0]?.textContent || '',
      };
    } catch (error) {
      mostrarNotificacao('erro', 'Erro ao carregar dados para edição');
      setTimeout(() => voltar(), 2000);
    }
  }
});

const salvarBebida = async () => {
  salvando.value = true;

  const xmlPayload = `
    <Bebida>
      <nome>${bebida.value.nome}</nome>
      <preco>${bebida.value.preco}</preco>
      <tipo>${bebida.value.tipo}</tipo>
      <descricao>${bebida.value.descricao}</descricao>
    </Bebida>
  `.trim();

  try {
    if (isEdicao.value) {
      await api.put(`/bebidas/${route.params.id}`, xmlPayload);
      mostrarNotificacao('sucesso', 'Bebida atualizada com sucesso!');
    } else {
      await api.post('/bebidas', xmlPayload);
      mostrarNotificacao('sucesso', 'Nova bebida adicionada com sucesso!');
    }
    
    setTimeout(() => voltar(), 1500);
  } catch (error) {
    mostrarNotificacao('erro', 'Erro ao salvar a bebida. Verifique as informações.');
    console.error(error);
  } finally {
    salvando.value = false;
  }
};

const mostrarNotificacao = (tipo, mensagem) => {
  notificacao.value = { mostrar: true, tipo, mensagem };
};

const voltar = () => router.push('/bebidas');
</script>

<style scoped>
.container {
  width: 100%;
  max-width: 600px;
  margin: 40px auto;
  background: #111;
  padding: 40px;
  border-top: 4px solid #e74c3c;
  box-shadow: 0 10px 50px rgba(0,0,0,0.8);
  border-radius: 4px;
  color: #eee;
}

h1 {
  text-align: center;
  color: #fff;
  font-family: 'Saira Extra Condensed', sans-serif;
  letter-spacing: 1px;
  margin: 0 0 10px 0;
  font-size: 2rem;
}

.divider {
  width: 60px;
  height: 3px;
  background: #e74c3c;
  margin: 0 auto 30px auto;
}

.form-group {
  margin-bottom: 24px;
}

.form-group label {
  display: block;
  color: #e74c3c;
  font-weight: bold;
  margin-bottom: 8px;
  text-transform: uppercase;
  font-size: 0.9rem;
}

.form-group input, 
.form-group textarea {
  width: 100%;
  padding: 12px;
  background: #080808;
  border: 1px solid #222;
  color: #fff;
  border-radius: 4px;
  box-sizing: border-box;
  font-size: 1rem;
  font-family: inherit;
}

.form-group input:focus, 
.form-group textarea:focus {
  outline: none;
  border-color: #e74c3c;
  box-shadow: 0 0 8px rgba(231,76,60,0.4);
}

.form-group textarea {
  resize: vertical;
  min-height: 100px;
}

.actions {
  display: flex;
  gap: 15px;
  margin-top: 30px;
}

.btn-primary,
.btn-secondary {
  flex: 1;
  padding: 14px;
  border: none;
  font-weight: bold;
  cursor: pointer;
  text-transform: uppercase;
  border-radius: 4px;
  font-size: 0.95rem;
  transition: background 0.2s;
}

.btn-primary {
  background: #e74c3c;
  color: #fff;
}

.btn-primary:hover:not(:disabled) {
  background: #c0392b;
}

.btn-primary:disabled {
  background: #555;
  cursor: not-allowed;
  opacity: 0.6;
}

.btn-secondary {
  background: #333;
  color: #fff;
}

.btn-secondary:hover {
  background: #555;
}
</style>