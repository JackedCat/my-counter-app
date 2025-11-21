<script>
  import { onMount } from 'svelte'

  const STORAGE_KEY = 'counter:local'
  let count = $state(0)
  let hasLoaded = $state(false)
  let showConfirm = $state(false)

  const clampToZero = (value) => Math.max(0, value)

  const loadSavedCount = () => {
    if (typeof localStorage === 'undefined') return null
    const saved = localStorage.getItem(STORAGE_KEY)
    if (saved === null) return null
    const parsed = Number.parseInt(saved, 10)
    if (Number.isFinite(parsed) && parsed >= 0) return parsed
    localStorage.removeItem(STORAGE_KEY)
    return null
  }

  onMount(() => {
    const savedCount = loadSavedCount()
    if (typeof savedCount === 'number') {
      count = clampToZero(savedCount)
    }
    hasLoaded = true
  })

  $effect(() => {
    if (!hasLoaded || typeof localStorage === 'undefined') return
    localStorage.setItem(STORAGE_KEY, String(count))
  })

  const increment = () => {
    count = clampToZero(count + 1)
  }

  const decrement = () => {
    count = clampToZero(count - 1)
  }

  const reset = () => {
    count = 0
  }

  const openConfirm = () => {
    if (count === 0) return
    showConfirm = true
  }

  const confirmReset = () => {
    reset()
    showConfirm = false
  }

  const closeConfirm = () => {
    showConfirm = false
  }
</script>

<main class="page">
  <header class="top-bar">
    <button class="icon-btn" aria-label="Configuration">
      <svg class="w-6 h-6 text-gray-800 dark:text-white" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="currentColor" viewBox="0 0 24 24">
    <path fill-rule="evenodd" d="M9.586 2.586A2 2 0 0 1 11 2h2a2 2 0 0 1 2 2v.089l.473.196.063-.063a2.002 2.002 0 0 1 2.828 0l1.414 1.414a2 2 0 0 1 0 2.827l-.063.064.196.473H20a2 2 0 0 1 2 2v2a2 2 0 0 1-2 2h-.089l-.196.473.063.063a2.002 2.002 0 0 1 0 2.828l-1.414 1.414a2 2 0 0 1-2.828 0l-.063-.063-.473.196V20a2 2 0 0 1-2 2h-2a2 2 0 0 1-2-2v-.089l-.473-.196-.063.063a2.002 2.002 0 0 1-2.828 0l-1.414-1.414a2 2 0 0 1 0-2.827l.063-.064L4.089 15H4a2 2 0 0 1-2-2v-2a2 2 0 0 1 2-2h.09l.195-.473-.063-.063a2 2 0 0 1 0-2.828l1.414-1.414a2 2 0 0 1 2.827 0l.064.063L9 4.089V4a2 2 0 0 1 .586-1.414ZM8 12a4 4 0 1 1 8 0 4 4 0 0 1-8 0Z" clip-rule="evenodd"/>
</svg>

    </button>
    <button class="icon-btn" aria-label="À propos de moi">
      <svg class="w-6 h-6 text-gray-800 dark:text-white" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="currentColor" viewBox="0 0 24 24">
        <path fill-rule="evenodd" d="M2 12C2 6.477 6.477 2 12 2s10 4.477 10 10-4.477 10-10 10S2 17.523 2 12Zm9.408-5.5a1 1 0 1 0 0 2h.01a1 1 0 1 0 0-2h-.01ZM10 10a1 1 0 1 0 0 2h1v3h-1a1 1 0 1 0 0 2h4a1 1 0 1 0 0-2h-1v-4a1 1 0 0 0-1-1h-2Z" clip-rule="evenodd"/>
      </svg>
    </button>
  </header>

  <section class="panel">
    <div class="value" aria-live="polite">{count}</div>

    <div class="controls">
      <button class="ghost" on:click={decrement} disabled={count === 0} aria-label="Diminuer">
        -
      </button>
      <button class="primary" on:click={increment} aria-label="Augmenter">
        +
      </button>
    </div>

    <button class="link" on:click={openConfirm} disabled={count === 0} aria-label="Réinitialiser">
      ↺
    </button>
  </section>

  {#if showConfirm}
    <div class="modal-backdrop" role="presentation" on:click={closeConfirm}>
      <div
        class="modal"
        role="dialog"
        aria-modal="true"
        aria-label="Confirmer la réinitialisation"
        on:click|stopPropagation
      >
        <p>Reset counter ?</p>
        <div class="modal-actions">
          <button class="ghost" on:click={closeConfirm}>Cancel</button>
          <button class="primary" on:click={confirmReset}>Reset</button>
        </div>
      </div>
    </div>
  {/if}
</main>
