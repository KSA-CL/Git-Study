<!--
  자식 컴포넌트: UserCard
  
  - defineProps()로 부모에서 받은 데이터를 선언
  - defineEmits()로 부모에게 보낼 이벤트를 선언
-->
<script setup>
import { computed } from 'vue'

// defineProps: 부모가 전달하는 데이터를 정의
// 타입과 기본값, 필수 여부 등을 설정할 수 있음
const props = defineProps({
  name: {
    type: String,
    required: true,
  },
  role: {
    type: String,
    default: 'Unknown',
  },
  level: {
    type: Number,
    default: 1,
  },
  active: {
    type: Boolean,
    default: true,
  },
})

// defineEmits: 이 컴포넌트가 부모에게 보낼 이벤트를 정의
const emit = defineEmits(['level-up', 'toggle-active'])

// Props 기반 computed
const levelPercent = computed(() => (props.level / 10) * 100)
const levelColor = computed(() => {
  if (props.level >= 7) return 'var(--color-secondary)'
  if (props.level >= 4) return 'var(--color-warning)'
  return 'var(--color-primary-light)'
})

const initials = computed(() => props.name.charAt(0))
</script>

<template>
  <div class="user-card" :class="{ inactive: !active }">
    <div class="card-header">
      <div
        class="avatar"
        :style="{ background: active ? levelColor : 'var(--color-text-muted)' }"
      >
        {{ initials }}
      </div>
      <div class="user-info">
        <h4>{{ name }}</h4>
        <span class="role">{{ role }}</span>
      </div>
      <span
        class="status-dot"
        :class="active ? 'online' : 'offline'"
      ></span>
    </div>

    <div class="level-section">
      <div class="level-header">
        <span>레벨</span>
        <span class="level-value" :style="{ color: levelColor }">{{ level }}</span>
      </div>
      <div class="level-bar">
        <div
          class="level-fill"
          :style="{
            width: levelPercent + '%',
            background: levelColor,
          }"
        ></div>
      </div>
    </div>

    <div class="card-actions">
      <!-- emit('이벤트이름')으로 부모에게 이벤트 발생 -->
      <button
        class="btn btn-level"
        :disabled="level >= 10 || !active"
        @click="emit('level-up')"
      >
        ⬆ 레벨업
      </button>
      <button
        class="btn btn-toggle"
        @click="emit('toggle-active')"
      >
        {{ active ? '🔴 비활성화' : '🟢 활성화' }}
      </button>
    </div>
  </div>
</template>

<style scoped>
.user-card {
  background: var(--color-bg-card);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  padding: var(--space-lg);
  transition: all var(--transition-normal);
}

.user-card:hover {
  border-color: var(--color-border-active);
  background: var(--color-bg-card-hover);
  transform: translateY(-2px);
}

.user-card.inactive {
  opacity: 0.6;
}

.card-header {
  display: flex;
  align-items: center;
  gap: var(--space-sm);
  margin-bottom: var(--space-md);
}

.avatar {
  width: 42px;
  height: 42px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 700;
  font-size: 1.1rem;
  color: white;
  flex-shrink: 0;
}

.user-info {
  flex: 1;
}

.user-info h4 {
  font-size: 0.95rem;
  font-weight: 600;
}

.role {
  font-size: 0.75rem;
  color: var(--color-text-muted);
}

.status-dot {
  width: 10px;
  height: 10px;
  border-radius: 50%;
}

.status-dot.online {
  background: var(--color-secondary);
  box-shadow: 0 0 8px var(--color-secondary-glow);
}

.status-dot.offline {
  background: var(--color-text-muted);
}

.level-section {
  margin-bottom: var(--space-md);
}

.level-header {
  display: flex;
  justify-content: space-between;
  font-size: 0.8rem;
  color: var(--color-text-secondary);
  margin-bottom: var(--space-xs);
}

.level-value {
  font-weight: 700;
  font-size: 0.9rem;
}

.level-bar {
  height: 6px;
  background: var(--color-bg-input);
  border-radius: 3px;
  overflow: hidden;
}

.level-fill {
  height: 100%;
  border-radius: 3px;
  transition: all var(--transition-slow);
}

.card-actions {
  display: flex;
  gap: var(--space-sm);
}

.btn {
  flex: 1;
  padding: 8px 12px;
  border: none;
  border-radius: var(--radius-sm);
  font-family: var(--font-family);
  font-size: 0.78rem;
  font-weight: 600;
  cursor: pointer;
  transition: all var(--transition-fast);
}

.btn:disabled {
  opacity: 0.4;
  cursor: not-allowed;
}

.btn-level {
  background: var(--color-surface);
  color: var(--color-text);
}

.btn-level:hover:not(:disabled) {
  background: var(--color-primary);
}

.btn-toggle {
  background: var(--color-surface);
  color: var(--color-text-secondary);
}

.btn-toggle:hover {
  color: var(--color-text);
}
</style>
