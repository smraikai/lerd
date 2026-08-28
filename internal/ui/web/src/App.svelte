<script lang="ts">
  import { onMount, onDestroy } from 'svelte';
  import NavRail from '$components/NavRail.svelte';
  import SidePanel from '$components/SidePanel.svelte';
  import MobileHeader from '$components/MobileHeader.svelte';
  import MobileNav from '$components/MobileNav.svelte';
  import MobileBackBar from '$components/MobileBackBar.svelte';
  import { routeRest, parseHash, type TabId } from '$stores/route';
  import { loadVersion } from '$stores/version';
  import { loadAccessMode } from '$stores/accessMode';
  import { loadStatus } from '$stores/status';
  import { loadPhpVersions } from '$stores/phpVersions';
  import { loadNodeVersions } from '$stores/nodeVersions';
  import { loadAutostart } from '$stores/autostart';
  import { loadIdle } from '$stores/idle';
  import { loadSites } from '$stores/sites';
  import { loadServices } from '$stores/services';
  import { loadServiceIcons } from '$stores/serviceIcons';
  import { loadFrameworkMarks } from '$stores/frameworkMarks';
  import { loadWorkerMarks } from '$stores/workerMarks';
  import { loadWorkerHealth } from '$stores/workerHealth';
  import { watchActiveRun } from '$stores/wizard';
  import { connectWs, disconnectWs } from '$lib/ws';
  import { initDashboardRoute } from '$stores/dashboard';
  import '$stores/activity';
  import { mobileView } from '$stores/mobileView';
  import ModalHost from './modals/ModalHost.svelte';
  import DashboardOverlay from '$components/DashboardOverlay.svelte';
  import WorkerHealthBanner from '$components/WorkerHealthBanner.svelte';
  import NotifyBanner from '$components/NotifyBanner.svelte';
  import NotificationToasts from '$components/NotificationToasts.svelte';
  import WizardBubble from '$components/WizardBubble.svelte';
  import CommandPalette from '$components/CommandPalette.svelte';
  import CommandRunModal from '$components/CommandRunModal.svelte';
  import { initNotify } from '$lib/notify';

  import SitesTab from '$tabs/SitesTab.svelte';
  import ServicesTab from '$tabs/ServicesTab.svelte';
  import SystemTab from '$tabs/SystemTab.svelte';
  import SitesDetail from '$tabs/SitesDetail.svelte';
  import ServicesDetail from '$tabs/ServicesDetail.svelte';
  import SystemDetail from '$tabs/SystemDetail.svelte';
  import AppsPage from '$tabs/AppsPage.svelte';
  import DashboardTab from '$tabs/DashboardTab.svelte';

  function handlePageHide() {
    disconnectWs();
  }

  // Keep the mounted desktop pane tied directly to browser history. The shared
  // route store drives navigation labels and deep links; this local view key
  // guarantees the content pane remounts on every top-level hash transition.
  let viewTab = $state<TabId>(parseHash(location.hash).tab);
  function handleRouteChange() {
    viewTab = parseHash(location.hash).tab;
  }

  onMount(() => {
    loadVersion();
    loadAccessMode();
    loadStatus();
    loadPhpVersions();
    loadNodeVersions();
    loadAutostart();
    loadIdle();
    loadSites();
    loadServices();
    loadServiceIcons();
    loadFrameworkMarks();
    loadWorkerMarks();
    loadWorkerHealth();
    // A scaffold or setup the wizard sent to the background outlives the page,
    // so the dashboard asks on load whether anything is still going.
    watchActiveRun();
    connectWs();
    initDashboardRoute();
    initNotify();
    window.addEventListener('pagehide', handlePageHide);
    window.addEventListener('hashchange', handleRouteChange);
  });

  onDestroy(() => {
    window.removeEventListener('pagehide', handlePageHide);
    window.removeEventListener('hashchange', handleRouteChange);
    disconnectWs();
  });

  // On mobile, show the detail pane once an item is selected (routeRest non-empty).
  // System tab always has a default selection (lerd) so we only show detail there
  // if the user explicitly picked something, to avoid jumping past the list.
  const showMobileDetail = $derived(Boolean($routeRest));
  const onApps = $derived($mobileView === 'apps');
  const onDashboard = $derived(viewTab === 'dashboard');
</script>

<div class="h-screen flex bg-[#f7f7f8] dark:bg-lerd-bg text-gray-800 dark:text-gray-200">
  <NavRail />

  {#if !onDashboard}
    <SidePanel>
      {#if viewTab === 'sites'}
        <SitesTab />
      {:else if viewTab === 'services'}
        <ServicesTab />
      {:else if viewTab === 'system'}
        <SystemTab />
      {/if}
    </SidePanel>
  {/if}

  <main class="flex-1 flex flex-col overflow-hidden">
    {#if !showMobileDetail}
      <MobileHeader />
    {/if}

    <div class="hidden md:flex flex-col flex-1 overflow-hidden">
      {#if onDashboard}<DashboardTab />{/if}
      {#if viewTab === 'sites'}<SitesDetail />{/if}
      {#if viewTab === 'services'}<ServicesDetail />{/if}
      {#if viewTab === 'system'}<SystemDetail />{/if}
    </div>

    {#if onApps}
      <div class="md:hidden flex-1 flex flex-col overflow-hidden pb-16">
        <AppsPage />
      </div>
    {:else if onDashboard}
      <div class="md:hidden flex-1 overflow-y-auto pb-16">
        <DashboardTab />
      </div>
    {:else if !showMobileDetail}
      <div class="md:hidden flex-1 overflow-y-auto pb-16">
        {#if viewTab === 'sites'}
          <SitesTab />
        {:else if viewTab === 'services'}
          <ServicesTab />
        {:else if viewTab === 'system'}
          <SystemTab />
        {/if}
      </div>
    {:else}
      <div class="md:hidden flex-1 flex flex-col overflow-hidden pb-16">
        <MobileBackBar />
        {#if viewTab === 'sites'}
          <SitesDetail />
        {:else if viewTab === 'services'}
          <ServicesDetail />
        {:else if viewTab === 'system'}
          <SystemDetail />
        {/if}
      </div>
    {/if}
  </main>

  <MobileNav />
  <ModalHost />
  <DashboardOverlay />
  <WorkerHealthBanner />
  <NotifyBanner />
  <NotificationToasts />
  <WizardBubble />
  <CommandPalette />
  <CommandRunModal />
</div>
