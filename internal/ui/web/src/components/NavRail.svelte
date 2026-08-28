<script lang="ts">
  import { onMount } from 'svelte';
  import { tab, goToTab, TABS, type TabId } from '$stores/route';
  import Icon, { type IconName } from './Icon.svelte';
  import RailLogo from './RailLogo.svelte';
  import ThemeSwitcher from './ThemeSwitcher.svelte';
  import NotificationCenter from './NotificationCenter.svelte';
  import ServiceIcon from './ServiceIcon.svelte';
  import VersionLabel from './VersionLabel.svelte';
  import {
    dashboardServices,
    dashboardOpen,
    openDashboard,
    openDocs,
    openProfiler
  } from '$stores/dashboard';
  import { dashboardIconSvg } from '$lib/dashboardIcons';
  import { profilerEnabled, loadProfilerStatus } from '$stores/profiler';
  import { serviceLabel } from '$stores/services';
  import { accessMode } from '$stores/accessMode';
  import { desktopAppInstalled, insideDesktopApp, openInDesktopApp } from '$lib/notify';
  import { m } from '../paraglide/messages.js';

  // "Open in app" shows only in a browser when the desktop app is installed.
  const inDesktopApp = insideDesktopApp();
  const showOpenInApp = $derived($desktopAppInstalled && !inDesktopApp);

  onMount(() => {
    void loadProfilerStatus();
  });

  // Hide host-local launchers only when dashboard-control authority is
  // unavailable. Authenticated remote dashboards receive authority.
  const remote = $derived(!$accessMode.localControl);

  const labels = $derived<Record<TabId, string>>({
    dashboard: m.nav_dashboard(),
    sites: m.nav_sites(),
    services: m.nav_services(),
    system: m.nav_system()
  });

  const icons: Record<TabId, IconName> = {
    dashboard: 'dashboard',
    sites: 'sites',
    services: 'services',
    system: 'system'
  };

  function navigate(t: TabId) {
    const needsDashboardReload = t === 'dashboard' && $tab !== 'dashboard';
    goToTab(t);
    // The dashboard has a different pane topology (no list sidebar). A hard
    // remount on return prevents Svelte from retaining the preceding detail
    // pane after that topology change.
    if (needsDashboardReload) window.location.reload();
  }
</script>

<aside
  class="hidden md:flex flex-col w-44 lg:w-48 shrink-0 bg-[#f3f3f4] dark:bg-[#141415] border-r border-gray-200 dark:border-lerd-border z-20"
>
  <div class="h-[65px] shrink-0 flex items-center px-3.5 border-b border-gray-200 dark:border-lerd-border">
    <RailLogo />
    <div class="ml-2 min-w-0">
      <div class="text-[13px] font-semibold leading-none text-gray-900 dark:text-white">Lerd</div>
      <div class="mt-1 text-[10px] leading-none text-gray-400 dark:text-gray-500">Local development</div>
    </div>
  </div>

  <nav class="flex flex-col gap-0.5 px-2 py-3" aria-label="Primary">
    {#each TABS as t (t)}
      <button
        type="button"
        aria-current={!$dashboardOpen && $tab === t ? 'page' : undefined}
        onclick={() => navigate(t)}
        class="h-8 w-full flex items-center gap-2.5 px-2.5 rounded-[5px] text-[12px] font-medium transition-colors
          {!$dashboardOpen && $tab === t
            ? 'bg-white text-gray-950 shadow-xs ring-1 ring-gray-200 dark:bg-white/10 dark:text-white dark:ring-white/10'
            : 'text-gray-600 hover:bg-black/[0.04] hover:text-gray-950 dark:text-gray-400 dark:hover:bg-white/[0.06] dark:hover:text-gray-100'}"
      >
        <span class="w-4 h-4 shrink-0 flex items-center justify-center"><Icon name={icons[t]} /></span>
        <span class="truncate">{labels[t]}</span>
      </button>
    {/each}
  </nav>

  {#if !remote}
    <div class="mx-2 pt-3 mt-1 border-t border-gray-200 dark:border-lerd-border">
      <div class="px-2.5 pb-1.5 text-[10px] font-semibold uppercase tracking-[0.08em] text-gray-400 dark:text-gray-600">Tools</div>
      <button
        type="button"
        onclick={openProfiler}
        class="h-8 w-full flex items-center gap-2.5 px-2.5 rounded-[5px] text-[12px] font-medium transition-colors
          {$dashboardOpen?.name === 'profiler'
            ? 'bg-white text-gray-950 shadow-xs ring-1 ring-gray-200 dark:bg-white/10 dark:text-white dark:ring-white/10'
            : 'text-gray-600 hover:bg-black/[0.04] dark:text-gray-400 dark:hover:bg-white/[0.06]'}"
      >
        <span class="relative flex w-4 h-4 items-center justify-center">
          <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            {@html dashboardIconSvg('profiler')}
          </svg>
        </span>
        <span>{m.nav_profiler()}</span>
        {#if $profilerEnabled}<span class="ml-auto text-[10px] text-emerald-600 dark:text-emerald-400">On</span>{/if}
      </button>
      {#each $dashboardServices as svc (svc.name)}
        <button
          type="button"
          onclick={() => openDashboard(svc)}
          class="h-8 w-full flex items-center gap-2.5 px-2.5 rounded-[5px] text-[12px] font-medium transition-colors
            {$dashboardOpen?.name === svc.name
              ? 'bg-white text-gray-950 shadow-xs ring-1 ring-gray-200 dark:bg-white/10 dark:text-white dark:ring-white/10'
              : 'text-gray-600 hover:bg-black/[0.04] dark:text-gray-400 dark:hover:bg-white/[0.06]'}"
        >
          <span class="w-4 h-4 shrink-0 flex items-center justify-center">
            <ServiceIcon
              name={svc.name}
              category={svc.category}
              icon={svc.icon}
              color={svc.color}
              preset={svc.preset}
              bare
              compact
              tint={false}
            />
          </span>
          <span class="truncate">{serviceLabel(svc.name)}</span>
        </button>
      {/each}
    </div>
  {/if}

  <div class="mt-auto border-t border-gray-200 dark:border-lerd-border p-2">
    <button
      type="button"
      onclick={openDocs}
      class="h-8 w-full flex items-center gap-2.5 px-2.5 rounded-[5px] text-[12px] font-medium text-gray-600 hover:bg-black/[0.04] dark:text-gray-400 dark:hover:bg-white/[0.06]"
    >
      <span class="w-4 h-4"><Icon name="docs" /></span>
      <span>{m.nav_documentation()}</span>
    </button>
    {#if showOpenInApp}
      <button type="button" onclick={openInDesktopApp} class="h-8 w-full px-2.5 text-left text-[12px] text-gray-600 dark:text-gray-400">
        {m.nav_open_in_app()}
      </button>
    {/if}
    <div class="mt-1 flex items-center gap-1 px-1">
      <NotificationCenter size="sm" />
      <ThemeSwitcher size="sm" />
      <div class="ml-auto max-w-20 overflow-hidden"><VersionLabel /></div>
    </div>
  </div>
</aside>
