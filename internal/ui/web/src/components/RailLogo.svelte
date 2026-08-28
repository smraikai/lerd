<script lang="ts">
  import { version } from '$stores/version';
  import { goToTab } from '$stores/route';
  import { tab } from '$stores/route';
  import { m } from '../paraglide/messages.js';
  import { tooltip } from '$lib/tooltip';

  const label = $derived(
    $version.hasUpdate ? m.system_lerd_updateAvailable() : m.nav_dashboard()
  );

  function openDashboard() {
    const needsReload = $tab !== 'dashboard';
    goToTab('dashboard');
    if (needsReload) window.location.reload();
  }
</script>

<button
  class="flex items-center justify-center relative rounded-[6px] p-0.5 transition-colors hover:bg-black/[0.04] dark:hover:bg-white/[0.06]"
  aria-label={label}
  use:tooltip={{ label, placement: 'right' }}
  onclick={openDashboard}
>
  <img src="/icons/icon-512.png?v=selected-l" class="w-7 h-7 object-contain" alt="Lerd" />
</button>
