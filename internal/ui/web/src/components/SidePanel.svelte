<script lang="ts">
  import type { Snippet } from 'svelte';

  interface Props {
    children: Snippet;
  }
  let { children }: Props = $props();
</script>

<div
  class="hidden md:flex flex-col w-52 lg:w-56 shrink-0 border-r border-gray-200 dark:border-lerd-border bg-white dark:bg-lerd-card overflow-hidden"
>
  {@render children()}
</div>
