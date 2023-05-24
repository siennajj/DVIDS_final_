<script>
  import {onMount} from 'svelte';
  import data from '/src/data/GPSstops.json';
  import data2 from '/src/data/carstops.json';
  import data3 from '/src/data/points_of_interest.json';

// State
  let car = 0;
  let PickCar_Name = "all";
  let Car_Overview = "";

  onMount(() => {
    const urlParams = new URLSearchParams(window.location.search);
    PickCar_Name = urlParams.get('param1');
    Car_Overview = urlParams.get('param2');
  });

  // Select which data to show
  function selectData(data, data2, data3) {
    if (PickCar_Name === "all") {
      return data;
    } else {
      return data.filter(item => item.car_name === PickCar_Name);
    }
  }
  
function CAL_LocationColor(type) {
    switch(type) {
      case "professional":
      case "catering":
      case "domestic":
      case "housing":
      default:
    }
  }

	const Map_Width = 600;
  const Map_Height = 600;
  const Latitudes = data.map(car => car.lat);
  const Longitudes = data.map(car => car.long);
  const Min_Latitude = Math.min(...Latitudes);
  const Max_Latitude = Math.max(...Latitudes);
  const Min_Longitude = Math.min(...Longitudes);
  const Max_Longitude = Math.max(...Longitudes);
  const Latitude_Range = Max_Latitude - Min_Latitude;
  const Longitude_Range = Max_Longitude - Min_Longitude;
  const LATITUDE_COORD_RATIO = Map_Height / Latitude_Range;
  const LONGITUDE_COORD_RATIO = Map_Width / Longitude_Range;
</script>






<main>

{#if Car_Overview}
<p><a href={`/Car Overview?param2=${encodeURIComponent(Car_Overview)}`}>Car Overview</a></p>
<p> <a href={`/Prevuous Car?param2=${encodeURIComponent(Car_Overview)}`}>Prevuous Car</a> <a href={`/Next Car?param2=${encodeURIComponent(Car_Overview)}`}>Next Car</a></p>
{/if}

<ul><b>Sienna Jeong - KU Leuven - r0881089 </b> </ul>

</main>
   
<div id="sliderDetails">
    <input type="range" min="0" max="20160" bind:value={car} step="1440" id="slider">
    <p id="sliderValue">{car}</p>
    
</div>

<svg width=600 height=600>
  <rect x="0" y="0" width="600" height="600" fill="#efefef" />
  {#each data as car}
    <circle
      cx={(car.long - Min_Longitude) * LONGITUDE_COORD_RATIO}
      cy={(Max_Latitude - car.lat) * LATITUDE_COORD_RATIO}
      r="2"
      opacity={car.car_id == PickCar_Name? '1' : '0.2'}
       />
  {/each}
</svg>    




