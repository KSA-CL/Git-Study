<!--
  자식 컴포넌트: AlertBox
  
  - Props로 알림 타입 전달
  - Slot으로 메시지 콘텐츠 삽입
  - Emit으로 닫기 이벤트 전달
-->
<script setup>
import { computed } from 'vue'

const props = defineProps({
  type: {
    type: String,
    default: 'info',
    validator: (val) => ['info', 'success', 'warning', 'error'].includes(val),
  },
})

const emit = defineEmits(['dismiss'])

const icon = computed(() => {
  const icons = {
    info: 'ℹ️',
    success: '✅',
    warning: '⚠️',
    error: '❌',
  }
  return icons[props.type]
})
</script>

<template>
  <div class="alert-box" :class="`alert-${type}`">
    <span class="alert-icon">{{ icon }}</span>
    <!-- slot: 부모 컴포넌트에서 전달한 콘텐츠가 여기에 삽입됨 -->
    <span class="alert-message">
      <slot>기본 알림 메시지</slot>
    </span>
    <button class="alert-close" @click="emit('dismiss')">✕</button>
  </div>
</template>

<style scoped>
.alert-box {
  display: flex;
  align-items: center;
  gap: var(--space-sm);
  padding: 10px 14px;
  border-radius: var(--radius-sm);
  font-size: 0.85rem;
  animation: slideIn 0.3s ease;
}

.alert-info {
  background: rgba(108, 99, 255, 0.12);
  border: 1px solid rgba(108, 99, 255, 0.25);
  color: var(--color-primary-light);
}

.alert-success {
  background: rgba(0, 212, 170, 0.12);
  border: 1px solid rgba(0, 212, 170, 0.25);
  color: var(--color-secondary);
}

.alert-warning {
  background: rgba(255, 200, 87, 0.12);
  border: 1px solid rgba(255, 200, 87, 0.25);
  color: var(--color-warning);
}

.alert-error {
  background: rgba(255, 87, 87, 0.12);
  border: 1px solid rgba(255, 87, 87, 0.25);
  color: var(--color-danger);
}

.alert-icon {
  font-size: 1rem;
  flex-shrink: 0;
}

.alert-message {
  flex: 1;
}

.alert-close {
  background: none;
  border: none;
  color: inherit;
  opacity: 0.6;
  cursor: pointer;
  font-size: 0.8rem;
  padding: 4px;
  transition: opacity var(--transition-fast);
}

.alert-close:hover {
  opacity: 1;
}

@keyframes slideIn {
  from {
    opacity: 0;
    transform: translateY(-8px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>
