<nav>
  <a href="/"> Car Overview</a>
</nav>

<script>
  import {onMount} from 'svelte';
  import data from '/src/data/GPSstops.json';
  import data2 from '/src/data/carstops.json';
  import data3 from '/src/data/points_of_interest.json';

// State
  let car = 0;
  let PickCar_Name = "";
  let carData = [];
  
  onMount(() => {
    const urlParams = new URLSearchParams(window.location.search);
    PickCar_Name = urlParams.get('param1');
    //Car_Overview = urlParams.get('param2');
    carData = selectData(data2, PickCar_Name);
  });

  // Select which data to show
  function selectData(data2, PickCar_Name) {
    if (PickCar_Name === "all") {
      return data2;
    } else {
      return data2.filter(item => item.car_name === PickCar_Name);
    }
  }
  function callCurrentCarIndex() {
    return carData.findIndex(car => car === PickCar_Name);
  }

  function callPreviousCar() {
    const currentIndex = callCurrentCarIndex();
    if (currentIndex > 0) {
      return carData[currentIndex - 1];
    }
    return null;
  }

  function callNextCar() {
    const currentIndex = callCurrentCarIndex();
    if (currentIndex < carData.length - 1) {
      return carData[currentIndex + 1];
    }
    return null;
  }

  function processCircleCoords(item) {
  
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

<style>
.container{
  display: flex;
  align-items: flex-start;
}

/*.gps-data {
/*would it be necessary?
}*/


</style>


<div class="container">
  

</div>

<div class="gps-image">
  <svg width="300" heigjt="300">
    <rect x="0" y="0" width="300" height="300" fill="#efefef" />
    {#each data as car}
      {#if car.car_id ===PickCar_Name}
    <circle
      cx={(car.long - Min_Longitude) * LONGITUDE_COORD_RATIO}
      cy={(Max_Latitude - car.lat) * LATITUDE_COORD_RATIO}
      r="2"
      fill="red"
    />
      {/if}
    {/each}
    {#each data3 as location}
      <g class="tooltip">
        <circle
          cx={(location.long - Min_Longitude) * LONGITUDE_COORD_RATIO}
          cy={(Max_Latitude - location.lat) * LATITUDE_COORD_RATIO}
          r="7"
          opacity = "1"
        />
        <title>{location.name}</title>
      </g>
    {/each}
  </svg>
</div>

 
  
<main>
{#if carData && carData.length > 0}
  {#each carData as car, index}
      {#if car.car_name === PickCar_Name}
        {#if index > 0}
          <a href={`/details?param1=${encodeURIComponent(carData[index - 1].car_name)}`}>Previous Car</a> 
        {/if}
        {#if index < carData.length - 1}
          <a href={`/details?param1=${encodeURIComponent(carData[index + 1].car_name)}`}>Next Car</a> 
        {/if}
      {/if} 
    {/each}
{/if}

<ul><b>Sienna Jeong - KU Leuven - r0881089 </b> </ul>

</main>
   
<div id="sliderDetails">
  <div class="slider-container">
    <input type="range" min="0" max="20160" bind:value={car} step="1440" id="slider">
    <p id="sliderValue"> </p>
  </div>    
</div>