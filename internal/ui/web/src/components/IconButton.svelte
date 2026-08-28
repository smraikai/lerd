<script lang="ts">
  import type { Snippet } from 'svelte';
  import { tooltip } from '$lib/tooltip';

  interface Props {
    title?: string;
    active?: boolean;
    onclick?: (e: MouseEvent) => void;
    size?: 'sm' | 'md' | 'lg';
    children: Snippet;
  }

  let { title, active = false, onclick, size = 'md', children }: Props = $props();

  const sizeMap: Record<NonNullable<Props['size']>, string> = {
    sm: 'w-7 h-7',
    md: 'w-8 h-8',
    lg: 'w-10 h-10'
  };

  const sizeClass = $derived(sizeMap[size]);
</script>

<button
  aria-label={title}
  {onclick}
  use:tooltip={{ label: title ?? '', placement: 'right' }}
  class="{sizeClass} rounded-[5px] flex items-center justify-center transition-colors {active
    ? 'bg-white shadow-xs ring-1 ring-gray-200 text-gray-900 dark:bg-white/10 dark:ring-white/10 dark:text-white'
    : 'text-gray-500 dark:text-gray-400 hover:bg-black/[0.04] dark:hover:bg-white/[0.06] hover:text-gray-800 dark:hover:text-gray-200'}"
>
  {@render children()}
</button>
