<script>
  import { onMount } from 'svelte'

  const STORAGE_KEY = 'counter:local'
  let count = $state(0)
  let hasLoaded = $state(false)

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
</script>

<main class="page">
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

    <button class="link" on:click={reset} disabled={count === 0} aria-label="Réinitialiser">
      ↺
    </button>
  </section>
</main>
