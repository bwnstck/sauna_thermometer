<script lang="ts">
  import sauna from "./assets/sauna.png";
  import { onMount } from "svelte";
  import addMinutes from "date-fns/addMinutes";
  import fromUnixTime from "date-fns/fromUnixTime";
  import format from "date-fns/format";
  import type { SaunaSession } from "./types/api";
  import axios from "axios";
  import Chart from "./components/Chart.svelte";
  import formatDistance from "date-fns/formatDistance";

  let saunaSessions: SaunaSession[] = [];
  let lastSession: SaunaSession = null;

  onMount(async () => {
    const resp = await axios.get("/api/sessions", {
      headers: {
        "Content-Type": "application/json;charset=UTF-8",
        "Access-Control-Allow-Origin": "*", // Could work and fix the previous problem, but not in all APIs
      },
    });
    // const resp = await fetch(`http://192.168.178.11/sessions`,);

    saunaSessions = resp.data as SaunaSession[];
    lastSession = saunaSessions[saunaSessions.length - 1];
  });

  const generateIndex = (i: number) => i + 1;
</script>

<main
  class="flex flex-col items-center justify-center bg-slate-800 min-h-screen"
>
  <h1 class="text-3xl text-slate-100">Sauna-Tacho</h1>
  <a href="/" class="logo sauna">
    <img src={sauna} class="" alt="Sauna Logo" />
  </a>
  {#if lastSession}
    <h2 class="text-5xl mb-5  text-slate-200">
      {lastSession
        ? `${lastSession.temperatures[lastSession.temperatures.length - 1]}º C`
        : "Loading Temp..."}
    </h2>
  {:else}
    <h3 class="h-full text-slate-200">Sauna offline</h3>
  {/if}

  {#if saunaSessions.length}
    {#each [...saunaSessions].reverse() as session, i}
      <h4>{generateIndex(i)}</h4>
      <h3 class="text-slate-200 mt-5">
        {format(fromUnixTime(session?.start_at), "dd.MM.yy")}
      </h3>

      <h3 class="text-slate-200">
        {formatDistance(
          fromUnixTime(session?.start_at),
          addMinutes(
            fromUnixTime(session?.start_at),
            session.temperatures.length
          )
        )}
      </h3>
      <Chart
        chartValues={session.temperatures.filter((item, index) => {
          if (index % 10 === 0) {
            return item;
          }
        })}
        startTime={fromUnixTime(session.start_at)}
      />
    {/each}
  {/if}
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
