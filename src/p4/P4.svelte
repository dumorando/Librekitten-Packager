<script>
  import {_} from '../locales/';
  import {fade} from 'svelte/transition';
  import Section from './Section.svelte';
  import SelectProject from './SelectProject.svelte';
  import SelectLocale from './SelectLocale.svelte';
  import SelectTheme from './SelectTheme.svelte';
  import Progress from './Progress.svelte';
  import Modals from './Modals.svelte';
  import News from './News.svelte';
  import {progress, theme, error} from './stores';
  import {isSupported, isSafari, isStandalone, version} from './environment';
  import {
    APP_NAME,
    FEEDBACK_PRIMARY,
    FEEDBACK_SECONDARY,
    ACCENT_COLOR,
    SOURCE_CODE,
    WEBSITE,
    DONATE,
    PRIVACY_POLICY
  } from '../packager/brand';

  let projectData;

  const darkMedia = window.matchMedia('(prefers-color-scheme: dark)');
  let systemTheme = darkMedia.matches ? 'dark' : 'light';
  if (darkMedia.addEventListener) {
    darkMedia.addEventListener('change', () => {
      systemTheme = darkMedia.matches ? 'dark' : 'light';
    });
  }
  $: document.documentElement.setAttribute('theme', $theme === 'system' ? systemTheme : $theme);

  let modalVisible = false;

  const defaultTitle = document.title;
  let title = '';
  $: document.title = projectData && title ? `${title} - ${APP_NAME}` : defaultTitle;

  const getPackagerOptionsComponent = () => import(
    /* webpackChunkName: "packager-options-ui" */
    './PackagerOptions.svelte'
  ).catch((err) => {
    $error = err;
  });

  // We know for sure we will need this component very soon, so start loading it immediately.
  getPackagerOptionsComponent();
</script>

<style>
  :root {
    font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
    background: white;
    color: black;
  }
  :global([theme="dark"]) {
    background: #111;
    color: #eee;
    color-scheme: dark;
  }
  :global(a) {
    color: blue;
  }
  :global([theme="dark"] a) {
    color: #56b2ff;
  }
  :global(a:active) {
    color: red;
  }
  :global(input[type="text"]),
  :global(input[type="number"]),
  :global(textarea) {
    padding: 2px;
  }
  :global(input[type="text"]),
  :global(input[type="number"]),
  :global(textarea),
  :global(.is-not-safari select) {
    background-color: white;
    color: black;
    border: 1px solid rgb(160, 160, 160);
    border-radius: 2px;
  }
  :global(.is-not-safari select:hover) {
    border-color: rgb(30, 30, 30);
  }
  :global([theme="dark"] input[type="text"]),
  :global([theme="dark"] input[type="number"]),
  :global([theme="dark"] textarea),
  :global([theme="dark"] .is-not-safari select) {
    background-color: #333;
    color: white;
    border-color: #888;
  }
  :global([theme="dark"] .is-not-safari select:hover) {
    border-color: #bbb;
  }
  :global(p), :global(h1), :global(h2), :global(h3) {
    margin: 12px 0;
  }
  :global(summary) {
    cursor: pointer;
  }
  :global(input) {
    font-size: 0.8em;
  }
  main {
    padding-bottom: 10px;
  }
  footer {
    text-align: center;
  }
  footer > div {
    margin-top: 12px;
  }

  .center {
    display: flex;
    justify-content: center;
    align-items: center;
  }
</style>

<Modals bind:modalVisible={modalVisible} />

<main aria-hidden={modalVisible} class:is-not-safari={!isSafari}>
  <Section>
    <div class="center">
      <img src="./librekitten.ico" alt="librekitty" />
      <h1>{APP_NAME}</h1>
    </div>
  </Section>

  {#if !isStandalone}
    <News />
  {/if}

  {#if isSupported}
    <SelectProject bind:projectData />
  {:else}
    <Section>
      <h2>{$_('p4.browserNotSupported')}</h2>
      <p>{$_('p4.browserNotSupportedDescription')}</p>
    </Section>
  {/if}

  {#if projectData}
    {#await getPackagerOptionsComponent()}
      <Section center>
        <Progress text={$_('p4.importingInterface')} />
      </Section>
    {:then { default: PackagerOptions }}
      <div in:fade>
        <PackagerOptions
          projectData={projectData}
          bind:title={title}
        />
      </div>
    {:catch}
      <Section center>
        <p>
          {$_('p4.unknownImportError')}
        </p>
      </Section>
    {/await}
  {/if}

  {#if $progress.visible}
    <Section center>
      <Progress progress={$progress.progress} text={$progress.text} />
    </Section>
  {/if}

  <footer>
    <div>
      {#if PRIVACY_POLICY && !isStandalone}
        <a href={PRIVACY_POLICY}>{$_('p4.privacy')}</a>
        <span> - </span>
      {/if}
      <a href={FEEDBACK_PRIMARY.link}>{$_('p4.feedback')}</a>
      {#if SOURCE_CODE}
        <span> - </span>
        <a href={SOURCE_CODE}>{$_('p4.sourceCode')}</a>
      {/if}
      {#if DONATE}
        <!-- Donation link needs to be wrapped in another element so we can hide it in the Mac App Store -->
        <span class="donate-link">
          <span> - </span>
          <a href={DONATE}>{$_('p4.donate')}</a>
        </span>
      {/if}
    </div>
    <div>
      <a href="https://docs.turbowarp.org/packager">{$_('p4.documentation')}</a>
    </div>
    <div>
      <SelectTheme />
    </div>
    <div>
      <SelectLocale />
    </div>
  </footer>
</main>
