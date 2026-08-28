<script lang="ts">
  import type { Snippet } from 'svelte';
  import { tooltip } from '$lib/tooltip';

  // Compact icon-button toggle shared by the debug bridge and profiler.

  interface Props {
    enabled: boolean;
    busy?: boolean;
    title?: string;
    onclick?: (e: MouseEvent) => void;
    children: Snippet;
  }

  let { enabled, busy = false, title, onclick, children }: Props = $props();
</script>

<button
  type="button"
  aria-label={title}
  use:tooltip={title ?? ''}
  {onclick}
  disabled={busy}
  aria-pressed={enabled}
  class="relative w-6 h-6 flex items-center justify-center rounded-sm transition-colors disabled:opacity-40
    {enabled
      ? 'text-emerald-600 dark:text-emerald-400 hover:bg-emerald-50 dark:hover:bg-emerald-900/20'
      : 'text-gray-400 hover:text-gray-600 dark:hover:text-gray-300 hover:bg-gray-100 dark:hover:bg-white/5'}"
>
  {@render children()}
</button>
