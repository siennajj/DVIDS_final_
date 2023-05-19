<script>
import {onMount} from 'svelte';
import data from '/src/data/GPSstops.json';
import data2 from '/src/data/carstops.json';
import data3 from '/src/data/points_of_interest.json';

onMount(() => {
  const searchParams = new URLSearchParams(window.location.search);
  selectedCarId = searchParams.get('param1');
});
/*
onMount(() => {
    const urlparams = new URLSearchParams(window.location.search);
    tr_data = urlparams.get('par1');
  });
*/
  let running = false;
  let interval = null;
</script>

<div id="sliderDetails">
  <input type="range" min="0" max="20160" value="0" step="1440" id="slider">
  <p id="sliderValue"></p>
</div>
   


