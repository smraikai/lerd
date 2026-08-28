<script lang="ts" module>
  export type CardTone = 'default' | 'critical' | 'warn';
</script>

<script lang="ts">
  import type { Snippet } from 'svelte';

  interface Props {
    title: string;
    tone?: CardTone;
    badge?: Snippet;
    footer?: Snippet;
    children: Snippet;
  }
  let { title, tone = 'default', badge, footer, children }: Props = $props();

  const accent: Record<CardTone, string> = {
    default: '',
    critical: 'border-l-4 border-l-red-500',
    warn: 'border-l-4 border-l-yellow-500'
  };
</script>

<!-- Below xl the grid stacks into many rows and the page scrolls, so the card
     keeps a readable band of its own. At xl it stretches to fill its grid row
     instead, and min-h-0 lets it shrink with that row rather than overflow. -->
<section class="flex flex-col min-h-[220px] max-h-[320px] bg-white dark:bg-lerd-card border border-gray-200 dark:border-lerd-border rounded-[6px] overflow-hidden shadow-[0_1px_2px_rgba(0,0,0,0.025)] {accent[tone]}">
  <div class="shrink-0 flex items-center justify-between gap-3 px-4 py-3 border-b border-gray-200 dark:border-lerd-border">
    <span class="text-[13px] font-semibold text-gray-900 dark:text-gray-100">{title}</span>
    {#if badge}{@render badge()}{/if}
  </div>
  <div class="flex-1 min-h-0 overflow-y-auto px-4 py-3.5 space-y-2.5">
    {@render children()}
  </div>
  {#if footer}
    <div class="shrink-0 px-4 py-2.5 border-t border-gray-200 dark:border-lerd-border bg-gray-50/60 dark:bg-white/[0.015]">
      {@render footer()}
    </div>
  {/if}
</section>
