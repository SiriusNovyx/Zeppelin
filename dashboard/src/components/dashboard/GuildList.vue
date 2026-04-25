<template>
  <!-- Loading skeleton -->
  <div v-if="loading" class="guild-page">
    <div class="page-header">
      <h1 class="page-title">Your Servers</h1>
    </div>
    <div class="guild-grid">
      <div class="guild-card guild-card--skeleton" v-for="n in 6" :key="n">
        <div class="skeleton-icon"></div>
        <div class="skeleton-lines">
          <div class="skeleton-line skeleton-line--wide"></div>
          <div class="skeleton-line skeleton-line--narrow"></div>
        </div>
      </div>
    </div>
  </div>

  <!-- Loaded state -->
  <div v-else class="guild-page">
    <div class="page-header">
      <h1 class="page-title">Your Servers</h1>
      <span class="guild-count">{{ guilds.length }} server{{ guilds.length !== 1 ? 's' : '' }}</span>
    </div>

    <div v-if="guilds.length === 0" class="empty-state">
      <div class="empty-icon">🏠</div>
      <div class="empty-title">No servers found</div>
      <div class="empty-desc">You don't have access to any servers yet. Ask your server owner to add you.</div>
    </div>

    <div v-else class="guild-grid">
      <div
        v-for="guild in guilds"
        :key="guild.id"
        class="guild-card"
      >
        <!-- Server icon -->
        <div class="guild-icon-wrap">
          <img
            v-if="guild.icon"
            class="guild-icon"
            :src="guild.icon"
            :alt="guild.name"
          />
          <div v-else class="guild-icon guild-icon--fallback">
            {{ guild.name.charAt(0).toUpperCase() }}
          </div>
        </div>

        <!-- Server info -->
        <div class="guild-info">
          <div class="guild-name" :title="guild.name">{{ guild.name }}</div>
          <div class="guild-id">{{ guild.id }}</div>
        </div>

        <!-- Actions -->
        <div class="guild-actions">
          <router-link
            class="action-btn action-btn--primary"
            :to="'/dashboard/guilds/' + guild.id + '/config'"
          >
            Config
          </router-link>
          <router-link
            v-if="canManageAccess(guild.id)"
            class="action-btn"
            :to="'/dashboard/guilds/' + guild.id + '/access'"
          >
            Access
          </router-link>
          <router-link
            v-if="canManageAccess(guild.id)"
            class="action-btn"
            :to="'/dashboard/guilds/' + guild.id + '/import-export'"
          >
            Import/Export
          </router-link>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { mapState } from "vuex";
import { ApiPermissions, hasPermission } from "@zeppelinbot/shared/apiPermissions.js";
import { AuthState, GuildState } from "../../store/types";

export default {
  async mounted() {
    await this.$store.dispatch("guilds/loadAvailableGuilds");
    await this.$store.dispatch("guilds/loadMyPermissionAssignments");
    this.loading = false;
  },
  data() {
    return { loading: true };
  },
  computed: {
    ...mapState('guilds', {
      guilds: (state: GuildState) => {
        const guilds = Array.from(state.available.values());
        guilds.sort((a, b) => {
          if (a.name > b.name) return 1;
          if (a.name < b.name) return -1;
          if (a.id > b.id) return 1;
          if (a.id < b.id) return -1;
          return 0;
        });
        return guilds;
      },
      guildPermissionAssignments: (state: GuildState) => state.guildPermissionAssignments,
    }),
    ...mapState('auth', {
      userId: (state: AuthState) => state.userId!,
    }),
  },
  methods: {
    canManageAccess(guildId: string) {
      const guildPermissions = this.guildPermissionAssignments[guildId] || [];
      const myPermissions = guildPermissions.find(p => p.type === "USER" && p.target_id === this.userId) || null;
      return myPermissions && hasPermission(new Set(myPermissions.permissions), ApiPermissions.ManageAccess);
    },
  },
};
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Syne:wght@600;700&family=DM+Sans:wght@300;400;500&display=swap');

/* ── Page ────────────────────────────────────────────────── */
.guild-page {
  font-family: 'DM Sans', sans-serif;
  animation: fadeIn 0.4s ease both;
}

.page-header {
  display: flex;
  align-items: baseline;
  gap: 0.85rem;
  margin-bottom: 1.75rem;
}

.page-title {
  font-family: 'Syne', sans-serif;
  font-size: 1.6rem;
  font-weight: 700;
  color: #e8ecf4;
  letter-spacing: -0.02em;
}

.guild-count {
  font-size: 0.85rem;
  color: #4b5572;
}

/* ── Grid ────────────────────────────────────────────────── */
.guild-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 0.875rem;
}

/* ── Card ────────────────────────────────────────────────── */
.guild-card {
  background: rgba(255, 255, 255, 0.03);
  border: 1px solid rgba(255, 255, 255, 0.07);
  border-radius: 14px;
  padding: 1.1rem 1.2rem;
  display: flex;
  align-items: center;
  gap: 1rem;
  transition: all 0.18s ease;
  animation: slideUp 0.4s ease both;
}

.guild-card:hover {
  background: rgba(255, 255, 255, 0.055);
  border-color: rgba(91, 111, 255, 0.25);
  transform: translateY(-2px);
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.2);
}

/* ── Icon ────────────────────────────────────────────────── */
.guild-icon-wrap {
  flex-shrink: 0;
}

.guild-icon {
  width: 46px;
  height: 46px;
  border-radius: 12px;
  object-fit: cover;
}

.guild-icon--fallback {
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, #2d3acc, #5b6fff);
  color: #fff;
  font-family: 'Syne', sans-serif;
  font-weight: 700;
  font-size: 1.1rem;
  border-radius: 12px;
  width: 46px;
  height: 46px;
}

/* ── Info ────────────────────────────────────────────────── */
.guild-info {
  flex: 1;
  min-width: 0;
}

.guild-name {
  font-weight: 500;
  font-size: 0.95rem;
  color: #e8ecf4;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  margin-bottom: 0.15rem;
}

.guild-id {
  font-size: 0.75rem;
  color: #4b5572;
  font-family: 'Courier New', monospace;
}

/* ── Actions ─────────────────────────────────────────────── */
.guild-actions {
  display: flex;
  flex-direction: column;
  gap: 0.3rem;
  flex-shrink: 0;
}

.action-btn {
  font-size: 0.78rem;
  font-weight: 500;
  padding: 0.3rem 0.75rem;
  border-radius: 6px;
  text-decoration: none;
  text-align: center;
  transition: all 0.15s ease;
  background: rgba(255, 255, 255, 0.06);
  color: #8b96b8;
  border: 1px solid rgba(255, 255, 255, 0.08);
  white-space: nowrap;
}

.action-btn:hover {
  background: rgba(255, 255, 255, 0.1);
  color: #c8d0e8;
}

.action-btn--primary {
  background: rgba(91, 111, 255, 0.15);
  color: #a8b4ff;
  border-color: rgba(91, 111, 255, 0.25);
}

.action-btn--primary:hover {
  background: rgba(91, 111, 255, 0.25);
  color: #c4cde8;
}

/* ── Empty state ─────────────────────────────────────────── */
.empty-state {
  text-align: center;
  padding: 4rem 2rem;
  color: #4b5572;
}

.empty-icon {
  font-size: 2.5rem;
  margin-bottom: 1rem;
}

.empty-title {
  font-size: 1.1rem;
  font-weight: 500;
  color: #7a86a8;
  margin-bottom: 0.5rem;
}

.empty-desc {
  font-size: 0.9rem;
  max-width: 360px;
  margin: 0 auto;
  line-height: 1.6;
}

/* ── Skeleton ────────────────────────────────────────────── */
.guild-card--skeleton {
  pointer-events: none;
}

.skeleton-icon {
  width: 46px;
  height: 46px;
  border-radius: 12px;
  background: rgba(255,255,255,0.06);
  flex-shrink: 0;
  animation: pulse 1.4s ease infinite;
}

.skeleton-lines {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.skeleton-line {
  height: 10px;
  border-radius: 4px;
  background: rgba(255,255,255,0.06);
  animation: pulse 1.4s ease infinite;
}

.skeleton-line--wide { width: 60%; }
.skeleton-line--narrow { width: 35%; }

/* ── Animations ──────────────────────────────────────────── */
@keyframes fadeIn {
  from { opacity: 0; }
  to   { opacity: 1; }
}

@keyframes slideUp {
  from { opacity: 0; transform: translateY(10px); }
  to   { opacity: 1; transform: translateY(0); }
}

@keyframes pulse {
  0%, 100% { opacity: 0.4; }
  50%       { opacity: 0.7; }
}
</style>
