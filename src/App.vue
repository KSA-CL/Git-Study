<!--
  📌 Vue 학습 데모 - 메인 App 컴포넌트

  이 프로젝트에서 배울 수 있는 Vue 핵심 개념:
  1. 데이터 바인딩 (ref, computed, v-model, v-bind)
  2. 조건부 & 리스트 렌더링 (v-if, v-show, v-for)
  3. 이벤트 핸들링 (@click, @keyup, 수식어)
  4. Props & Emit (부모-자식 통신)
  5. 라이프사이클 & Watch (onMounted, watch, watchEffect)
-->
<script setup>
import { ref } from 'vue'
import DataBinding from './components/01_DataBinding.vue'
import ConditionalList from './components/02_ConditionalList.vue'
import EventHandling from './components/03_EventHandling.vue'
import PropsEmit from './components/04_PropsEmit.vue'
import LifecycleWatch from './components/05_LifecycleWatch.vue'

const sections = [
  { id: 'binding', label: '데이터 바인딩', icon: '🔗' },
  { id: 'conditional', label: '조건부 & 리스트', icon: '📋' },
  { id: 'events', label: '이벤트 핸들링', icon: '🎯' },
  { id: 'props', label: 'Props & Emit', icon: '🔄' },
  { id: 'lifecycle', label: '라이프사이클', icon: '⏱' },
]

const activeSection = ref('binding')
</script>

<template>
  <div class="app-container">
    <!-- Header -->
    <header class="app-header">
      <div class="header-content">
        <div class="logo-area">
          <div class="logo">
            <span class="logo-icon">⚡</span>
            <span class="logo-text">Vue</span>
          </div>
          <div class="title-area">
            <h1>Vue.js 학습 데모</h1>
            <p class="subtitle">Composition API · 핵심 개념을 실습하며 배우기</p>
          </div>
        </div>
        <a
          href="https://vuejs.org/guide/introduction.html"
          target="_blank"
          class="docs-link"
        >
          📖 Vue 공식 문서
        </a>
      </div>
    </header>

    <!-- Navigation -->
    <nav class="section-nav">
      <button
        v-for="section in sections"
        :key="section.id"
        class="nav-btn"
        :class="{ active: activeSection === section.id }"
        @click="activeSection = section.id"
      >
        <span class="nav-icon">{{ section.icon }}</span>
        <span class="nav-label">{{ section.label }}</span>
      </button>
    </nav>

    <!-- Content -->
    <main class="app-main">
      <Transition name="fade" mode="out-in">
        <DataBinding v-if="activeSection === 'binding'" key="binding" />
        <ConditionalList v-else-if="activeSection === 'conditional'" key="conditional" />
        <EventHandling v-else-if="activeSection === 'events'" key="events" />
        <PropsEmit v-else-if="activeSection === 'props'" key="props" />
        <LifecycleWatch v-else-if="activeSection === 'lifecycle'" key="lifecycle" />
      </Transition>
    </main>

    <!-- Footer -->
    <footer class="app-footer">
      <p>
        💡 각 컴포넌트의 소스 코드(<code>src/components/</code>)를 열어 주석을 읽으며 학습하세요!
      </p>
    </footer>
  </div>
</template>

<style scoped>
.app-container {
  max-width: 1100px;
  margin: 0 auto;
  padding: var(--space-lg);
  min-height: 100vh;
}

/* Header */
.app-header {
  margin-bottom: var(--space-xl);
}

.header-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.logo-area {
  display: flex;
  align-items: center;
  gap: var(--space-md);
}

.logo {
  display: flex;
  align-items: center;
  gap: 6px;
  background: linear-gradient(135deg, var(--color-primary), var(--color-secondary));
  padding: 8px 16px;
  border-radius: var(--radius-md);
}

.logo-icon {
  font-size: 1.3rem;
}

.logo-text {
  font-size: 1.1rem;
  font-weight: 800;
  color: white;
  letter-spacing: -0.5px;
}

.title-area h1 {
  font-size: 1.5rem;
  font-weight: 800;
  background: linear-gradient(135deg, var(--color-text), var(--color-primary-light));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.subtitle {
  font-size: 0.8rem;
  color: var(--color-text-muted);
  margin-top: 2px;
}

.docs-link {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 8px 16px;
  background: var(--color-bg-card);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-sm);
  color: var(--color-text-secondary);
  text-decoration: none;
  font-size: 0.82rem;
  font-weight: 500;
  transition: all var(--transition-fast);
}

.docs-link:hover {
  border-color: var(--color-primary);
  color: var(--color-primary-light);
  box-shadow: var(--shadow-glow-primary);
}

/* Navigation */
.section-nav {
  display: flex;
  gap: var(--space-sm);
  margin-bottom: var(--space-xl);
  padding: 4px;
  background: var(--color-bg-card);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  overflow-x: auto;
}

.nav-btn {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 10px 18px;
  border: none;
  border-radius: var(--radius-md);
  background: transparent;
  color: var(--color-text-muted);
  font-family: var(--font-family);
  font-size: 0.82rem;
  font-weight: 500;
  cursor: pointer;
  transition: all var(--transition-normal);
  white-space: nowrap;
}

.nav-btn:hover {
  color: var(--color-text);
  background: var(--color-surface);
}

.nav-btn.active {
  background: linear-gradient(135deg, var(--color-primary), var(--color-primary-light));
  color: white;
  box-shadow: var(--shadow-glow-primary);
}

.nav-icon {
  font-size: 1rem;
}

.nav-label {
  font-weight: 600;
}

/* Main */
.app-main {
  min-height: 500px;
}

/* Footer */
.app-footer {
  margin-top: var(--space-2xl);
  padding: var(--space-lg);
  text-align: center;
  border-top: 1px solid var(--color-border);
}

.app-footer p {
  font-size: 0.82rem;
  color: var(--color-text-muted);
}

.app-footer code {
  background: var(--color-surface);
  padding: 2px 8px;
  border-radius: 4px;
  font-size: 0.8rem;
  color: var(--color-primary-light);
}

/* Fade Transition */
.fade-enter-active,
.fade-leave-active {
  transition: all 0.25s ease;
}

.fade-enter-from {
  opacity: 0;
  transform: translateY(12px);
}

.fade-leave-to {
  opacity: 0;
  transform: translateY(-12px);
}

@media (max-width: 768px) {
  .header-content {
    flex-direction: column;
    align-items: flex-start;
    gap: var(--space-md);
  }

  .section-nav {
    gap: var(--space-xs);
  }

  .nav-btn {
    padding: 8px 12px;
    font-size: 0.78rem;
  }

  .nav-label {
    display: none;
  }

  .nav-icon {
    font-size: 1.2rem;
  }
}
</style>
