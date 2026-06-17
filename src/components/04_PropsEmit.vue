<!--
  📌 04. Props & Emit (부모-자식 컴포넌트 통신)
  
  핵심 개념:
  - defineProps() : 부모 → 자식 데이터 전달
  - defineEmits() : 자식 → 부모 이벤트 전달
  - 단방향 데이터 흐름 (One-way Data Flow)
  - 슬롯(Slots): 부모가 자식 컴포넌트 내부에 콘텐츠 삽입
-->
<script setup>
import { ref } from 'vue'
import UserCard from './sub/UserCard.vue'
import AlertBox from './sub/AlertBox.vue'

// 부모 컴포넌트에서 관리하는 데이터
const users = ref([
  { id: 1, name: '김철수', role: 'Frontend', level: 3, active: true },
  { id: 2, name: '이영희', role: 'Backend', level: 5, active: true },
  { id: 3, name: '박민수', role: 'DevOps', level: 2, active: false },
])

const alerts = ref([])
let alertId = 0

// 자식 컴포넌트에서 올라온 이벤트 처리
function handleLevelUp(userId) {
  const user = users.value.find(u => u.id === userId)
  if (user && user.level < 10) {
    user.level++
    addAlert('success', `${user.name}님의 레벨이 ${user.level}로 올랐습니다!`)
  }
}

function handleToggleActive(userId) {
  const user = users.value.find(u => u.id === userId)
  if (user) {
    user.active = !user.active
    addAlert(
      user.active ? 'info' : 'warning',
      `${user.name}님이 ${user.active ? '활성화' : '비활성화'}되었습니다.`
    )
  }
}

function addAlert(type, message) {
  const id = alertId++
  alerts.value.push({ id, type, message })
  setTimeout(() => {
    alerts.value = alerts.value.filter(a => a.id !== id)
  }, 3000)
}

function dismissAlert(id) {
  alerts.value = alerts.value.filter(a => a.id !== id)
}
</script>

<template>
  <div class="demo-section">
    <div class="section-header">
      <span class="section-number">04</span>
      <div>
        <h2>Props & Emit</h2>
        <p class="section-desc">부모↔자식 통신, defineProps, defineEmits, Slots</p>
      </div>
    </div>

    <!-- 알림 영역: AlertBox는 Slot을 사용하는 컴포넌트 -->
    <TransitionGroup name="alert" tag="div" class="alerts-container">
      <AlertBox
        v-for="alert in alerts"
        :key="alert.id"
        :type="alert.type"
        @dismiss="dismissAlert(alert.id)"
      >
        <!-- 이 부분이 Slot으로 전달되는 콘텐츠 -->
        {{ alert.message }}
      </AlertBox>
    </TransitionGroup>

    <div class="demo-grid">
      <!--
        UserCard 컴포넌트에 Props로 데이터를 전달하고,
        Emit으로 이벤트를 받아서 처리
      -->
      <UserCard
        v-for="user in users"
        :key="user.id"
        :name="user.name"
        :role="user.role"
        :level="user.level"
        :active="user.active"
        @level-up="handleLevelUp(user.id)"
        @toggle-active="handleToggleActive(user.id)"
      />
    </div>

    <div class="code-hint">
      <h4>💡 데이터 흐름 이해하기</h4>
      <div class="flow-diagram">
        <div class="flow-box parent">부모 (App)</div>
        <div class="flow-arrow">
          <span class="arrow-down">→ Props (데이터)</span>
          <span class="arrow-up">← Emit (이벤트)</span>
        </div>
        <div class="flow-box child">자식 (UserCard)</div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.demo-section {
  margin-bottom: var(--space-2xl);
}

.section-header {
  display: flex;
  align-items: center;
  gap: var(--space-md);
  margin-bottom: var(--space-lg);
}

.section-number {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 48px;
  height: 48px;
  background: linear-gradient(135deg, var(--color-warning), #ffe066);
  border-radius: var(--radius-md);
  font-size: 1.1rem;
  font-weight: 700;
  color: #1a1a2e;
  flex-shrink: 0;
}

.section-header h2 {
  font-size: 1.4rem;
  font-weight: 700;
}

.section-desc {
  font-size: 0.85rem;
  color: var(--color-text-muted);
  margin-top: 2px;
}

.alerts-container {
  position: relative;
  margin-bottom: var(--space-md);
  display: flex;
  flex-direction: column;
  gap: var(--space-sm);
}

.demo-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: var(--space-lg);
  margin-bottom: var(--space-lg);
}

/* Code Hint */
.code-hint {
  background: var(--color-bg-card);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  padding: var(--space-lg);
}

.code-hint h4 {
  font-size: 0.9rem;
  font-weight: 600;
  margin-bottom: var(--space-md);
}

.flow-diagram {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: var(--space-lg);
}

.flow-box {
  padding: 12px 24px;
  border-radius: var(--radius-md);
  font-size: 0.85rem;
  font-weight: 600;
  text-align: center;
}

.flow-box.parent {
  background: linear-gradient(135deg, rgba(108, 99, 255, 0.2), rgba(108, 99, 255, 0.1));
  border: 1px solid rgba(108, 99, 255, 0.3);
  color: var(--color-primary-light);
}

.flow-box.child {
  background: linear-gradient(135deg, rgba(0, 212, 170, 0.2), rgba(0, 212, 170, 0.1));
  border: 1px solid rgba(0, 212, 170, 0.3);
  color: var(--color-secondary);
}

.flow-arrow {
  display: flex;
  flex-direction: column;
  gap: 4px;
  font-size: 0.75rem;
}

.arrow-down {
  color: var(--color-primary-light);
}

.arrow-up {
  color: var(--color-secondary);
}

/* Alert animation */
.alert-enter-active,
.alert-leave-active {
  transition: all 0.3s ease;
}

.alert-enter-from {
  opacity: 0;
  transform: translateY(-10px);
}

.alert-leave-to {
  opacity: 0;
  transform: translateX(30px);
}

@media (max-width: 768px) {
  .demo-grid {
    grid-template-columns: 1fr;
  }
  .flow-diagram {
    flex-direction: column;
  }
}
</style>
