
<script>
import {afterUpdate} from 'svelte';
import toDataURL from 'canvas-background';
import Switch from './Switch.svelte';
import Susceptible from './OverallSusceptible.svelte';
import Infectious from './OverallInfectious.svelte';
import Recovered from './OverallRecovered.svelte';


export let resultsOverall={};

console.log(resultsOverall[1][0])
let susceptibleBool=true;
let infectiousBool=false;
let recoveredBool=false;
</script>


<Switch bind:checked={susceptibleBool}></Switch> Susceptible Overall Trend
<Switch bind:checked={infectiousBool}></Switch> Infectious Overall Trend
<Switch bind:checked={recoveredBool}></Switch> Recovered Overall Trend

{#if susceptibleBool}
<Susceptible {resultsOverall}/>
{/if}
{#if infectiousBool}
<Infectious {resultsOverall}/>
{/if}
{#if recoveredBool}
<Recovered {resultsOverall}/>
{/if}


<style>
</style>