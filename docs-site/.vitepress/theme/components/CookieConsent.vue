<script setup lang="ts">
import { ref, onMounted } from 'vue'

const showBanner = ref(false)
const isDark = ref(false)

const CLARITY_ID = 'vqyz7pn45j'

onMounted(() => {
  // Check dark mode
  isDark.value = document.documentElement.classList.contains('dark')
  
  // Check if user already made choice
  const consent = localStorage.getItem('mailtester-cookie-consent')
  if (!consent) {
    showBanner.value = true
  } else if (consent === 'accepted') {
    loadClarity()
  }
})

function loadClarity() {
  // Load Microsoft Clarity script
  if (window.clarity) return // Already loaded
  
  const w = window as any
  w.clarity = function(...args: any[]) {
    (w.clarity.q = w.clarity.q || []).push(args)
  }
  w.clarity.q = []
  
  const script = document.createElement('script')
  script.src = `https://www.clarity.ms/tag/${CLARITY_ID}`
  script.async = true
  script.defer = true
  document.head.appendChild(script)
}

function acceptCookies() {
  localStorage.setItem('mailtester-cookie-consent', 'accepted')
  showBanner.value = false
  loadClarity()
}

function rejectCookies() {
  localStorage.setItem('mailtester-cookie-consent', 'rejected')
  showBanner.value = false
}
</script>

<template>
  <Transition name="slide-up">
    <div v-if="showBanner" class="cookie-banner" :class="{ dark: isDark }">
      <div class="cookie-content">
        <span class="cookie-icon">📊</span>
        <p class="cookie-text">
          We use <strong>Microsoft Clarity</strong> for analytics to understand how visitors use our docs.
          Your data is anonymized and we don't collect personal information.
        </p>
        <div class="cookie-buttons">
          <button class="btn-accept" @click="acceptCookies">Accept</button>
          <button class="btn-reject" @click="rejectCookies">Decline</button>
        </div>
      </div>
    </div>
  </Transition>
</template>

<style scoped>
.cookie-banner {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  background: var(--bauhaus-white, #FAFAFA);
  border-top: 3px solid var(--bauhaus-black, #0D0D0D);
  padding: 16px 24px;
  z-index: 9999;
  font-family: 'DM Sans', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
}

.cookie-banner.dark {
  background: #1A1A1A;
  border-top-color: #444;
}

.cookie-content {
  max-width: 1200px;
  margin: 0 auto;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 20px;
  flex-wrap: wrap;
}

.cookie-icon {
  font-size: 1.5rem;
  flex-shrink: 0;
}

.cookie-text {
  flex: 1;
  min-width: 280px;
  margin: 0;
  font-size: 0.9rem;
  line-height: 1.5;
  color: var(--bauhaus-black, #0D0D0D);
}

.cookie-banner.dark .cookie-text {
  color: #FAFAFA;
}

.cookie-text strong {
  color: var(--bauhaus-red, #E63946);
}

.cookie-buttons {
  display: flex;
  gap: 12px;
  flex-shrink: 0;
}

.cookie-buttons button {
  font-family: 'DM Sans', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  font-weight: 700;
  font-size: 0.85rem;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  padding: 10px 24px;
  cursor: pointer;
  border-width: 3px;
  border-style: solid;
  border-radius: 0;
  transition: all 0.2s ease;
}

.btn-accept {
  background: var(--bauhaus-red, #E63946);
  color: white;
  border-color: var(--bauhaus-black, #0D0D0D);
}

.btn-accept:hover {
  background: #C1121F;
  transform: translate(-2px, -2px);
  box-shadow: 4px 4px 0 var(--bauhaus-black, #0D0D0D);
}

.dark .btn-accept {
  border-color: #444;
}

.dark .btn-accept:hover {
  box-shadow: 4px 4px solid #666;
}

.btn-reject {
  background: transparent;
  color: var(--bauhaus-black, #0D0D0D);
  border-color: var(--bauhaus-black, #0D0D0D);
}

.btn-reject:hover {
  background: var(--bauhaus-black, #0D0D0D);
  color: white;
}

.dark .btn-reject {
  color: #FAFAFA;
  border-color: #666;
}

.dark .btn-reject:hover {
  background: #666;
  color: #0D0D0D;
}

/* Animation */
.slide-up-enter-active,
.slide-up-leave-active {
  transition: transform 0.3s ease, opacity 0.3s ease;
}

.slide-up-enter-from,
.slide-up-leave-to {
  transform: translateY(100%);
  opacity: 0;
}

/* Responsive */
@media (max-width: 768px) {
  .cookie-content {
    flex-direction: column;
    text-align: center;
  }
  
  .cookie-buttons {
    width: 100%;
    justify-content: center;
  }
}
</style>
