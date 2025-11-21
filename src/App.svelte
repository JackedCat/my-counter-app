<script>
  import { onMount } from 'svelte'

  const STORAGE_KEY = 'counter:local'
  const STORAGE_LIMIT = 'counter:limit'
  const STORAGE_STEP = 'counter:step'
  let count = $state(0)
  let hasLoaded = $state(false)
  let showConfirm = $state(false)
  let showInfo = $state(false)
  let showSettings = $state(false)
  let maxLimit = $state(null)
  let step = $state(1)
  let configValue = $state('')
  let configLimit = $state('')
  let configStep = $state('')

  const clampValue = (value) => {
    const nonNegative = Math.max(0, value)
    if (maxLimit === null) return nonNegative
    return Math.min(nonNegative, maxLimit)
  }

  const loadSavedCount = () => {
    if (typeof localStorage === 'undefined') return null
    const saved = localStorage.getItem(STORAGE_KEY)
    if (saved === null) return null
    const parsed = Number.parseInt(saved, 10)
    if (Number.isFinite(parsed) && parsed >= 0) return parsed
    localStorage.removeItem(STORAGE_KEY)
    return null
  }

  const loadSavedLimit = () => {
    if (typeof localStorage === 'undefined') return null
    const saved = localStorage.getItem(STORAGE_LIMIT)
    if (saved === null) return null
    const parsed = Number.parseInt(saved, 10)
    if (Number.isFinite(parsed) && parsed >= 0) return parsed
    localStorage.removeItem(STORAGE_LIMIT)
    return null
  }

  const loadSavedStep = () => {
    if (typeof localStorage === 'undefined') return null
    const saved = localStorage.getItem(STORAGE_STEP)
    if (saved === null) return null
    const parsed = Number.parseInt(saved, 10)
    if (Number.isFinite(parsed) && parsed > 0) return parsed
    localStorage.removeItem(STORAGE_STEP)
    return null
  }

  onMount(() => {
    const savedLimit = loadSavedLimit()
    if (typeof savedLimit === 'number') {
      maxLimit = savedLimit
    }
    const savedStep = loadSavedStep()
    if (typeof savedStep === 'number') {
      step = savedStep
    }
    const savedCount = loadSavedCount()
    if (typeof savedCount === 'number') {
      count = clampValue(savedCount)
    }
    hasLoaded = true
  })

  $effect(() => {
    if (!hasLoaded || typeof localStorage === 'undefined') return
    if (maxLimit === null) {
      localStorage.removeItem(STORAGE_LIMIT)
    } else {
      localStorage.setItem(STORAGE_LIMIT, String(maxLimit))
    }
    localStorage.setItem(STORAGE_STEP, String(step))
    localStorage.setItem(STORAGE_KEY, String(count))
  })

  const increment = () => {
    count = clampValue(count + step)
  }

  const decrement = () => {
    count = clampValue(count - step)
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

  const openInfo = () => {
    showInfo = true
  }

  const closeInfo = () => {
    showInfo = false
  }

  const openSettings = () => {
    configValue = String(count)
    configLimit = maxLimit === null ? '' : String(maxLimit)
    configStep = String(step)
    showSettings = true
  }

  const closeSettings = () => {
    showSettings = false
  }

  const applySettings = () => {
    const parsedValue = Number.parseInt(configValue, 10)
    const parsedLimit = configLimit === '' ? null : Number.parseInt(configLimit, 10)
    const parsedStep = Number.parseInt(configStep, 10)

    const nextLimit = Number.isFinite(parsedLimit) && parsedLimit >= 0 ? parsedLimit : null
    maxLimit = nextLimit

    const nextStep = Number.isFinite(parsedStep) && parsedStep > 0 ? parsedStep : step
    step = nextStep

    const nextValue = Number.isFinite(parsedValue) ? parsedValue : count
    count = clampValue(nextValue)
    showSettings = false
  }

  const remaining = $derived(maxLimit === null ? null : Math.max(0, maxLimit - count))
</script>

<main class="page">
  <header class="top-bar">
    <button class="icon-btn" aria-label="Configuration" on:click={openSettings}>
      <svg class="w-6 h-6 text-gray-800 dark:text-white" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="currentColor" viewBox="0 0 24 24">
          <path fill-rule="evenodd" d="M9.586 2.586A2 2 0 0 1 11 2h2a2 2 0 0 1 2 2v.089l.473.196.063-.063a2.002 2.002 0 0 1 2.828 0l1.414 1.414a2 2 0 0 1 0 2.827l-.063.064.196.473H20a2 2 0 0 1 2 2v2a2 2 0 0 1-2 2h-.089l-.196.473.063.063a2.002 2.002 0 0 1 0 2.828l-1.414 1.414a2 2 0 0 1-2.828 0l-.063-.063-.473.196V20a2 2 0 0 1-2 2h-2a2 2 0 0 1-2-2v-.089l-.473-.196-.063.063a2.002 2.002 0 0 1-2.828 0l-1.414-1.414a2 2 0 0 1 0-2.827l.063-.064L4.089 15H4a2 2 0 0 1-2-2v-2a2 2 0 0 1 2-2h.09l.195-.473-.063-.063a2 2 0 0 1 0-2.828l1.414-1.414a2 2 0 0 1 2.827 0l.064.063L9 4.089V4a2 2 0 0 1 .586-1.414ZM8 12a4 4 0 1 1 8 0 4 4 0 0 1-8 0Z" clip-rule="evenodd"/>
      </svg>
    </button>
    
    <button class="icon-btn" aria-label="About me" on:click={openInfo}>
      <svg class="w-6 h-6 text-gray-800 dark:text-white" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="currentColor" viewBox="0 0 24 24">
        <path fill-rule="evenodd" d="M2 12C2 6.477 6.477 2 12 2s10 4.477 10 10-4.477 10-10 10S2 17.523 2 12Zm9.408-5.5a1 1 0 1 0 0 2h.01a1 1 0 1 0 0-2h-.01ZM10 10a1 1 0 1 0 0 2h1v3h-1a1 1 0 1 0 0 2h4a1 1 0 1 0 0-2h-1v-4a1 1 0 0 0-1-1h-2Z" clip-rule="evenodd"/>
      </svg>
    </button>
  </header>

  <section class="panel">
    <div class="value" aria-live="polite">{count}</div>

    {#if maxLimit !== null}
      <p class="meta">Remaining until limit: {remaining}</p>
    {/if}

    <div class="controls">
      <button class="ghost" on:click={decrement} disabled={count === 0} aria-label="Decrease">
        -
      </button>
      <button class="primary" on:click={increment} aria-label="Increase">
        +
      </button>
    </div>

    <button class="link" on:click={openConfirm} disabled={count === 0} aria-label="Reset">
      ↺
    </button>
  </section>

  {#if showConfirm}
    <div class="modal-backdrop" role="presentation" on:click={closeConfirm}>
      <div
        class="modal"
        role="dialog"
        aria-modal="true"
        aria-label="Confirm reset"
        on:click|stopPropagation
      >
        <p>Reset counter?</p>
        <div class="modal-actions">
          <button class="ghost" on:click={closeConfirm}>Cancel</button>
          <button class="primary" on:click={confirmReset}>Reset</button>
        </div>
      </div>
    </div>
  {/if}

  {#if showInfo}
    <div class="modal-backdrop" role="presentation" on:click={closeInfo}>
      <div class="modal" role="dialog" aria-modal="true" aria-label="About" on:click|stopPropagation>
        <div class="modal-header">
          <p class="modal-title">Simple counter</p>
          <button class="close-btn" aria-label="Close" on:click={closeInfo}>×</button>
        </div>
        <p class="modal-text">
          Minimal counter that retains your value on this device.
          <a class="modal-link" href="https://joshuanavas.com" target="_blank" rel="noreferrer">Learn more</a>
        </p>
        <div class="modal-actions">
          <a class="buy-coffee" href="https://www.buymeacoffee.com/joshnvs" target="_blank" rel="noreferrer">
            <svg class="w-6 h-6 text-gray-800 dark:text-white" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="none" viewBox="0 0 24 24">
              <path fill="currentColor" d="M11 3c0-.55228-.4477-1-1-1-.55228 0-1 .44772-1 1 0 .35778-.09872.50986-.19828.61606-.14832.15821-.36754.28572-.7723.50159l-.04537.02415c-.34513.18363-.84839.45139-1.24483.87426C6.27628 5.50986 6 6.15778 6 7c0 .55228.44772 1 1 1 .55229 0 1-.44772 1-1 0-.35778.09873-.50986.19829-.61606.14832-.15821.36754-.28572.7723-.50159l.04536-.02415c.34514-.18363.8484-.45139 1.24485-.87426C10.7237 4.49014 11 3.84222 11 3Zm5 0c0-.55228-.4477-1-1-1s-1 .44772-1 1c0 .35778-.0987.50986-.1983.61606-.1483.15821-.3675.28572-.7723.50159l-.0453.02415c-.3452.18363-.8484.45139-1.2449.87426C11.2763 5.50986 11 6.15778 11 7c0 .55228.4477 1 1 1s1-.44772 1-1c0-.35778.0987-.50986.1983-.61606.1483-.15821.3675-.28572.7723-.50159l.0454-.02415c.3451-.18363.8484-.45139 1.2448-.87426C15.7237 4.49014 16 3.84222 16 3Z"/>
              <path fill="currentColor" fill-rule="evenodd" d="M5 10c-.28252 0-.55187.1195-.74145.329-.18958.2095-.2817.4894-.25358.7705l.6398 6.398C4.90037 20.0535 7.0512 22 9.61995 22h.76015c2.3975 0 4.431-1.6957 4.8992-4H17c1.6569 0 3-1.3431 3-3s-1.3431-3-3-3h-1.095l.09-.9005c.0282-.2811-.064-.561-.2535-.7705-.1896-.2095-.459-.329-.7415-.329H5Zm12 6h-1.495l.2-2H17c.5523 0 1 .4477 1 1s-.4477 1-1 1Z" clip-rule="evenodd"/>
            </svg>
            Buy me a coffee
          </a>
          <button class="primary" on:click={closeInfo}>Close</button>
        </div>
        <p class="modal-foot">Created by Joshua Navas</p>
      </div>
    </div>
  {/if}

  {#if showSettings}
    <div class="modal-backdrop" role="presentation" on:click={closeSettings}>
      <div class="modal" role="dialog" aria-modal="true" aria-label="Settings" on:click|stopPropagation>
        <div class="modal-header">
          <p class="modal-title">Settings</p>
          <button class="close-btn" aria-label="Close" on:click={closeSettings}>×</button>
        </div>
        <div class="form">
          <label>
            Counter value
            <input type="number" min="0" bind:value={configValue} />
          </label>
          <label>
            Limit (optional)
            <input type="number" min="0" placeholder="None" bind:value={configLimit} />
          </label>
          <label>
            Step for + / -
            <input type="number" min="1" bind:value={configStep} />
          </label>
        </div>
        <div class="modal-actions">
          <button class="ghost" on:click={closeSettings}>Cancel</button>
          <button class="primary" on:click={applySettings}>Apply</button>
        </div>
      </div>
    </div>
  {/if}
</main>
