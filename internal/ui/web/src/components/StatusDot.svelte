<script lang="ts" module>
  export type StatusColor = 'green' | 'red' | 'yellow' | 'gray' | 'blue' | 'amber' | 'violet' | 'sky' | 'indigo' | 'emerald';
</script>

<script lang="ts">
  import Icon, { type IconName } from './Icon.svelte';

  interface Props {
    color: StatusColor;
    size?: 'xs' | 'sm' | 'md';
    pulse?: boolean;
  }
  let { color, size = 'sm', pulse = false }: Props = $props();

  const colorClass: Record<StatusColor, string> = {
    green: 'text-emerald-600 dark:text-emerald-400',
    emerald: 'text-emerald-600 dark:text-emerald-400',
    red: 'text-red-500',
    yellow: 'text-amber-500',
    gray: 'text-gray-400 dark:text-gray-500',
    blue: 'text-blue-500',
    amber: 'text-amber-500',
    violet: 'text-violet-500',
    sky: 'text-sky-500',
    indigo: 'text-indigo-500'
  };

  const sizeClass: Record<NonNullable<Props['size']>, string> = {
    xs: 'w-3 h-3',
    sm: 'w-3.5 h-3.5',
    md: 'w-4 h-4'
  };

  const iconName = $derived<IconName>(color === 'red' || color === 'yellow' || color === 'amber' ? 'warn' : color === 'gray' ? 'stop' : 'check');
  const iconClass = $derived(`${sizeClass[size]} ${colorClass[color]} shrink-0${pulse ? ' opacity-80' : ''}`);
</script>

<Icon name={iconName} class={iconClass} />
