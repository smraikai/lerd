<script lang="ts">
  import { onMount } from 'svelte';
  import { sites, sitesLoaded } from '$stores/sites';
  import { openLinkModal, openPresetModal } from '$stores/modals';
  import { openDocs } from '$stores/dashboard';
  import { accessMode } from '$stores/accessMode';
  import { m } from '../../paraglide/messages.js';

  const KEY = 'lerd-onboarding-dismissed';

  let dismissed = $state(false);

  onMount(() => {
    try {
      dismissed = localStorage.getItem(KEY) === '1';
    } catch {
      dismissed = false;
    }
  });

  function dismiss() {
    dismissed = true;
    try {
      localStorage.setItem(KEY, '1');
    } catch {
      /* incognito or storage disabled */
    }
  }

  const visible = $derived($sitesLoaded && $sites.length === 0 && !dismissed);
</script>

{#if visible}
  <section class="relative rounded-[6px] bg-white dark:bg-lerd-card border border-gray-200 dark:border-lerd-border shadow-[0_1px_2px_rgba(0,0,0,0.025)] overflow-hidden">
    <button
      type="button"
      onclick={dismiss}
      title={m.onboarding_dismiss()}
      aria-label={m.onboarding_dismiss()}
      class="absolute top-3 right-3 w-7 h-7 inline-flex items-center justify-center rounded-[4px] text-gray-400 hover:text-gray-700 dark:hover:text-gray-200 hover:bg-gray-100 dark:hover:bg-white/10 transition-colors"
    >
      <svg class="w-4 h-4" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" aria-hidden="true">
        <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12"/>
      </svg>
    </button>

    <div class="px-4 py-3 border-b border-gray-200 dark:border-lerd-border">
      <h2 class="text-[13px] font-semibold text-gray-900 dark:text-white">{m.onboarding_title()}</h2>
      <p class="text-xs text-gray-500 dark:text-gray-400 mt-0.5">{m.onboarding_subtitle()}</p>
    </div>

    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 divide-y sm:divide-y-0 sm:divide-x divide-gray-200 dark:divide-lerd-border">
      <div class="px-4 py-3.5">
        <div class="flex items-center gap-2 mb-2">
          <span class="w-5 h-5 inline-flex items-center justify-center rounded-[4px] bg-gray-100 dark:bg-white/[0.07] text-gray-600 dark:text-gray-300 text-[10px] font-semibold">1</span>
          <span class="text-sm font-semibold text-gray-800 dark:text-gray-100">{m.onboarding_park_title()}</span>
        </div>
        <p class="text-xs text-gray-500 dark:text-gray-400 mb-2">{m.onboarding_park_body()}</p>
        <code class="block text-[11px] font-mono bg-gray-100 dark:bg-black/20 border border-gray-200 dark:border-lerd-border text-gray-700 dark:text-gray-300 rounded-[4px] px-2 py-1.5 overflow-x-auto">lerd park ~/Code</code>
      </div>

      <div class="px-4 py-3.5 flex flex-col">
        <div class="flex items-center gap-2 mb-2">
          <span class="w-5 h-5 inline-flex items-center justify-center rounded-[4px] bg-gray-100 dark:bg-white/[0.07] text-gray-600 dark:text-gray-300 text-[10px] font-semibold">2</span>
          <span class="text-sm font-semibold text-gray-800 dark:text-gray-100">{m.onboarding_link_title()}</span>
        </div>
        <p class="text-xs text-gray-500 dark:text-gray-400 mb-3 flex-1">{m.onboarding_link_body()}</p>
        {#if $accessMode.localControl}
          <button
            type="button"
            onclick={openLinkModal}
            class="self-start inline-flex items-center gap-1.5 h-7 px-2.5 rounded-[4px] text-xs font-medium bg-lerd-red hover:bg-lerd-redhov text-white shadow-xs transition-colors"
          >
            <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24" aria-hidden="true">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4"/>
            </svg>
            {m.onboarding_link_cta()}
          </button>
        {:else}
          <p class="text-[11px] text-gray-400 dark:text-gray-500">{m.onboarding_loopbackOnly()}</p>
        {/if}
      </div>

      <div class="px-4 py-3.5 flex flex-col">
        <div class="flex items-center gap-2 mb-2">
          <span class="w-5 h-5 inline-flex items-center justify-center rounded-[4px] bg-gray-100 dark:bg-white/[0.07] text-gray-600 dark:text-gray-300 text-[10px] font-semibold">3</span>
          <span class="text-sm font-semibold text-gray-800 dark:text-gray-100">{m.onboarding_service_title()}</span>
        </div>
        <p class="text-xs text-gray-500 dark:text-gray-400 mb-3 flex-1">{m.onboarding_service_body()}</p>
        {#if $accessMode.localControl}
          <button
            type="button"
            onclick={openPresetModal}
            class="self-start inline-flex items-center gap-1.5 h-7 px-2.5 rounded-[4px] text-xs font-medium bg-white hover:bg-gray-50 dark:bg-white/[0.06] dark:hover:bg-white/10 border border-gray-200 dark:border-lerd-border text-gray-700 dark:text-gray-200 shadow-xs transition-colors"
          >{m.onboarding_service_cta()}</button>
        {:else}
          <p class="text-[11px] text-gray-400 dark:text-gray-500">{m.onboarding_loopbackOnly()}</p>
        {/if}
      </div>
    </div>

    <div class="flex items-center gap-3 px-4 py-2.5 border-t border-gray-200 dark:border-lerd-border bg-gray-50/60 dark:bg-white/[0.015] text-xs text-gray-500 dark:text-gray-400">
      <button
        type="button"
        onclick={openDocs}
        class="font-medium text-lerd-red hover:text-lerd-redhov transition-colors"
      >{m.onboarding_docs()}</button>
      <span class="text-gray-300 dark:text-gray-600">·</span>
      <span>{m.onboarding_dismissHint()}</span>
    </div>
  </section>
{/if}
