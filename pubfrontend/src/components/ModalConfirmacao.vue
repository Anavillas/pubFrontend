<template>
  <div class="modal-overlay" @click.self="$emit('cancelar')">
    <div class="modal-content">
      <div class="modal-header">
        <h3>⚠️ {{ titulo }}</h3>
      </div>
      <p class="modal-message">{{ mensagem }}</p>
      <div class="modal-actions">
        <button @click="$emit('cancelar')" class="btn-cancelar">
          CANCELAR
        </button>
        <button @click="$emit('confirmar')" class="btn-confirmar">
          CONFIRMAR
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  titulo: { type: String, default: 'Confirmação' },
  mensagem: { type: String, required: true }
});

defineEmits(['confirmar', 'cancelar']);
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.85);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 9999;
  animation: fadeIn 0.2s;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

.modal-content {
  background: #111;
  border: 2px solid #e74c3c;
  border-radius: 8px;
  padding: 30px;
  max-width: 450px;
  width: 90%;
  box-shadow: 0 20px 60px rgba(231, 76, 60, 0.3);
  animation: slideUp 0.3s;
}

@keyframes slideUp {
  from { transform: translateY(30px); opacity: 0; }
  to { transform: translateY(0); opacity: 1; }
}

.modal-header h3 {
  color: #e74c3c;
  margin: 0 0 20px 0;
  font-family: 'Saira Extra Condensed', sans-serif;
  letter-spacing: 1px;
  font-size: 1.5rem;
}

.modal-message {
  color: #eee;
  margin-bottom: 30px;
  line-height: 1.6;
  font-size: 1rem;
}

.modal-actions {
  display: flex;
  gap: 15px;
  justify-content: flex-end;
}

.btn-cancelar,
.btn-confirmar {
  padding: 12px 24px;
  border: none;
  font-weight: bold;
  cursor: pointer;
  border-radius: 4px;
  text-transform: uppercase;
  transition: all 0.2s;
  font-size: 0.9rem;
}

.btn-cancelar {
  background: #555;
  color: #fff;
}

.btn-cancelar:hover {
  background: #666;
}

.btn-confirmar {
  background: #e74c3c;
  color: #fff;
}

.btn-confirmar:hover {
  background: #c0392b;
}
</style>