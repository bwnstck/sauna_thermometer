<script lang="ts">
  import svelteLogo from './assets/svelte.svg';
  import sauna from './assets/sauna.png';
  import {onMount} from 'svelte';
  import addMinutes from 'date-fns/addMinutes';
  import fromUnixTime from 'date-fns/fromUnixTime';
  import format from 'date-fns/format';
  import type {SaunaSession} from './types/api';
  import axios from 'axios';

  let saunaSessions: SaunaSession[] = [];
  let lastSession: SaunaSession = null;
  let lastTemperature: number = null;
  let minutesSinceStart: number = null;
  let formattedTime: string = null;

  onMount(async () => {
    const resp = await axios.get('/api/sessions');

    saunaSessions = resp.data as SaunaSession[];
    lastSession = saunaSessions[saunaSessions.length - 1];
    minutesSinceStart = lastSession?.temperatures.length;

    const timeStamp = addMinutes(
      fromUnixTime(lastSession?.start_at),
      minutesSinceStart
    );
    const currentTemperatures = lastSession?.temperatures;

    lastTemperature = currentTemperatures[currentTemperatures.length - 1];
    formattedTime = format(timeStamp, 'dd.MM H:m');
  });
</script>

<main class="flex flex-col items-center justify-center h-screen">
  <h1 class="text-3xl text-slate-100">Sauna-Tacho</h1>
  <a href="#" class="logo sauna">
    <img src={sauna} class="" alt="Sauna Logo" />
  </a>
  <h2 class="text-slate-200">
    {lastTemperature ? `${lastTemperature}º C` : 'Loading Temp...'}
  </h2>
  <p class="text-slate-400">
    {lastSession?.temperatures?.length ? formattedTime : 'Loading Date ...'}
  </p>
</main>

<style>
  .logo {
    height: 6em;
    padding: 1.5em;
    will-change: filter;
  }
  .logo:hover {
    filter: drop-shadow(0 0 2em #646cffaa);
  }
  .logo.sauna:hover {
    filter: drop-shadow(0 0 2em #ff3e00aa);
  }
</style>
