<template>
  <div class="layout">
    <aside class="sidebar">
      <h2>🍺 Pub Manager</h2>
      <router-link to="/bebidas" class="menu-link">
        🍺 Cardápio do Bar
      </router-link>
      
      <button @click="logout" class="btn-logout">
        🚪 Sair
      </button>
    </aside>

    <div class="main">
      <header class="header">
        <h1>Painel</h1>
      </header>

      <router-view />
    </div>
  </div>
</template>

<script setup>
import { useRouter } from 'vue-router';

const router = useRouter();

const logout = () => {
  if (confirm('Tem certeza que deseja sair?')) {
    localStorage.removeItem('token');
    router.push('/login');
  }
};
</script>

<style scoped>
.layout {
  display: flex;
  min-height: 100vh;
  background: #000;
}

.sidebar {
  width: 250px;
  background: #0d0d0d;
  color: white;
  padding: 20px;
  border-right: 2px solid #1f1f1f;
  display: flex;
  flex-direction: column;
  align-items: stretch;
}

.sidebar h2 {
  margin-bottom: 30px;
  color: #fff;
  font-size: 1.5rem;
}

.menu-link {
  display: block;
  color: #ffffff;
  text-decoration: none;
  padding: 12px 16px;
  border-radius: 6px;
  transition: background 0.2s;
  margin-bottom: 8px;
}

.menu-link:hover,
.menu-link.router-link-active {
  background: #1f1f1f;
}

.btn-logout {
  margin-top: auto;
  width: 100%;
  background: #e74c3c;
  color: white;
  border: none;
  padding: 12px;
  border-radius: 6px;
  cursor: pointer;
  font-weight: bold;
  text-transform: uppercase;
  transition: background 0.2s;
  font-size: 0.9rem;
}

.btn-logout:hover {
  background: #c0392b;
}

.main {
  flex: 1;
  display: flex;
  flex-direction: column;
  background: radial-gradient(circle at top, #1a1a1a 0%, #000000 100%);
}

.header {
  background: #1a1a1a;
  color: white;
  padding: 20px 40px;
  border-bottom: 2px solid #222;
}

.header h1 {
  margin: 0;
  font-size: 1.5rem;
  font-weight: 600;
}
</style>