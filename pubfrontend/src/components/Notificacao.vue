<template>
  <transition name="slide">
    <div v-if="mostrar" :class="['notificacao', tipo]">
      <span class="icon">{{ tipo === 'sucesso' ? '✓' : '✗' }}</span>
      <span class="mensagem">{{ mensagem }}</span>
      <button @click="$emit('fechar')" class="btn-fechar">✕</button>
    </div>
  </transition>
</template>

<script setup>
defineProps({
  mostrar: { type: Boolean, default: false },
  tipo: { type: String, default: 'sucesso' },
  mensagem: { type: String, required: true }
});

defineEmits(['fechar']);
</script>

<style scoped>
.notificacao {
  position: fixed;
  top: 20px;
  right: 20px;
  padding: 16px 20px;
  border-radius: 6px;
  display: flex;
  align-items: center;
  gap: 12px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
  z-index: 10000;
  min-width: 320px;
  border-left: 4px solid;
}

.notificacao.sucesso {
  background: #1a1a1a;
  border-left-color: #2ecc71;
  color: #2ecc71;
}

.notificacao.erro {
  background: #1a1a1a;
  border-left-color: #e74c3c;
  color: #e74c3c;
}

.icon {
  font-size: 20px;
  font-weight: bold;
}

.mensagem {
  flex: 1;
  color: #eee;
  font-size: 0.95rem;
}

.btn-fechar {
  background: none;
  border: none;
  color: #888;
  cursor: pointer;
  font-size: 18px;
  padding: 0;
  transition: color 0.2s;
}

.btn-fechar:hover {
  color: #fff;
}

.slide-enter-active,
.slide-leave-active {
  transition: all 0.3s ease;
}

.slide-enter-from {
  transform: translateX(100%);
  opacity: 0;
}

.slide-leave-to {
  transform: translateX(100%);
  opacity: 0;
}
</style>