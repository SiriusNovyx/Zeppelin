<template>
  <div class="dashboard-root">
    <Title title="Alice — Dashboard" />

    <!-- Sidebar -->
    <aside class="sidebar">
      <div class="sidebar-logo">
        <img src="/img/logo.png" alt="Alice" class="sidebar-logo-img" />
        <span class="sidebar-logo-text">Alice</span>
      </div>

      <nav class="sidebar-nav" aria-label="Main navigation">
        <router-link to="/dashboard" class="nav-item" active-class="nav-item--active">
          <span class="nav-icon">⊞</span>
          <span>Servers</span>
        </router-link>
        <a href="/docs" class="nav-item">
          <span class="nav-icon">📖</span>
          <span>Docs</span>
        </a>
      </nav>

      <div class="sidebar-footer">
        <button class="nav-item nav-item--danger" @click="logout">
          <span class="nav-icon">↩</span>
          <span>Log out</span>
        </button>
      </div>
    </aside>

    <!-- Mobile top bar -->
    <header class="topbar">
      <div class="topbar-left">
        <img src="/img/logo.png" alt="Alice" class="topbar-logo" />
        <span class="topbar-title">Alice</span>
      </div>
      <div class="topbar-right">
        <router-link to="/dashboard" class="topbar-link">Servers</router-link>
        <a href="/docs" class="topbar-link">Docs</a>
        <button class="topbar-logout" @click="logout">Log out</button>
      </div>
    </header>

    <!-- Main content -->
    <main class="main-area">
      <div class="main-content">
        <router-view></router-view>
      </div>
    </main>
  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Syne:wght@600;700&family=DM+Sans:wght@300;400;500&display=swap');

/* ── Root layout ─────────────────────────────────────────── */
.dashboard-root {
  display: flex;
  min-height: 100vh;
  background: #080b14;
  color: #c8d0e8;
  font-family: 'DM Sans', sans-serif;
}

/* ── Sidebar ─────────────────────────────────────────────── */
.sidebar {
  width: 220px;
  flex-shrink: 0;
  background: rgba(255, 255, 255, 0.026);
  border-right: 1px solid rgba(255, 255, 255, 0.06);
  display: flex;
  flex-direction: column;
  padding: 1.5rem 0.75rem;
  position: fixed;
  top: 0;
  left: 0;
  height: 100vh;
  z-index: 30;
}

.sidebar-logo {
  display: flex;
  align-items: center;
  gap: 0.6rem;
  padding: 0.25rem 0.75rem 1.75rem;
}

.sidebar-logo-img {
  width: 32px;
  height: 32px;
  border-radius: 8px;
}

.sidebar-logo-text {
  font-family: 'Syne', sans-serif;
  font-weight: 700;
  font-size: 1.15rem;
  color: #fff;
  letter-spacing: -0.02em;
}

.sidebar-nav {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 0.2rem;
}

.sidebar-footer {
  padding-top: 1rem;
  border-top: 1px solid rgba(255,255,255,0.06);
}

.nav-item {
  display: flex;
  align-items: center;
  gap: 0.65rem;
  padding: 0.6rem 0.85rem;
  border-radius: 8px;
  font-size: 0.9rem;
  font-weight: 400;
  color: #7a86a8;
  text-decoration: none;
  transition: all 0.15s ease;
  cursor: pointer;
  border: none;
  background: none;
  width: 100%;
  text-align: left;
}

.nav-item:hover {
  background: rgba(255, 255, 255, 0.06);
  color: #c8d0e8;
}

.nav-item--active {
  background: rgba(91, 111, 255, 0.14);
  color: #a8b4ff;
}

.nav-item--active:hover {
  background: rgba(91, 111, 255, 0.2);
}

.nav-item--danger {
  color: #7a86a8;
}

.nav-item--danger:hover {
  background: rgba(220, 60, 60, 0.1);
  color: #f08080;
}

.nav-icon {
  font-size: 1rem;
  width: 1.2rem;
  text-align: center;
  flex-shrink: 0;
}

/* ── Mobile topbar ───────────────────────────────────────── */
.topbar {
  display: none;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 40;
  background: rgba(8, 11, 20, 0.92);
  backdrop-filter: blur(12px);
  border-bottom: 1px solid rgba(255, 255, 255, 0.07);
  padding: 0 1rem;
  height: 56px;
  align-items: center;
  justify-content: space-between;
}

.topbar-left {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.topbar-logo {
  width: 28px;
  height: 28px;
  border-radius: 7px;
}

.topbar-title {
  font-family: 'Syne', sans-serif;
  font-weight: 700;
  font-size: 1rem;
  color: #fff;
}

.topbar-right {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.topbar-link {
  font-size: 0.85rem;
  color: #7a86a8;
  text-decoration: none;
  padding: 0.3rem 0.6rem;
  border-radius: 6px;
  transition: color 0.15s;
}

.topbar-link:hover { color: #c8d0e8; }

.topbar-logout {
  font-size: 0.85rem;
  color: #7a86a8;
  background: none;
  border: none;
  cursor: pointer;
  padding: 0.3rem 0.6rem;
  border-radius: 6px;
  transition: color 0.15s;
}

.topbar-logout:hover { color: #f08080; }

/* ── Main content area ───────────────────────────────────── */
.main-area {
  flex: 1;
  margin-left: 220px;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.main-content {
  flex: 1;
  padding: 2.5rem 2rem;
  max-width: 1100px;
  width: 100%;
  margin: 0 auto;
}

/* ── Responsive ──────────────────────────────────────────── */
@media (max-width: 768px) {
  .sidebar { display: none; }
  .topbar { display: flex; }
  .main-area { margin-left: 0; padding-top: 56px; }
  .main-content { padding: 1.5rem 1rem; }
}
</style>

<script>
import Title from "../Title.vue";

export default {
  components: { Title },
  methods: {
    async logout() {
      await this.$store.dispatch("auth/logout");
      window.location.pathname = '/';
    },
  },
};
</script>
