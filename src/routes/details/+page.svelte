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
  
  // Select which data to show
  function selectData(data) {
    if (PickCar_Name === "all") {
      return data;
    } else {
      return data.filter(item => item.car_name === PickCar_Name);
    }
  }

  onMount(() => {
    const urlParams = new URLSearchParams(window.location.search);
    PickCar_Name = urlParams.get('param1');
    //Car_Overview = urlParams.get('param2');
    carData = selectData(data);
    carIndex = carData.findIndex(car => car.car_name === PickCar_Name);
  });

  function callPreviousCar() {
    if (carIndex > 0) {
      return carData[carIndex - 1];
    }
    return null;
  }

  function callNextCar() {
    if (carIndex < carData.length - 1) {
      return carData[carIndex + 1];
    }
    return null;
  }
  function CAL_LocationColor(type) {
    switch(type) {
      case "professional":
        return "red";
      case "catering":
        return "green";
      case "domestic":
        return "orange";
      case "housing":
        return "purple";
      default:
        return "blue";
    }
  }

	const Map_Width = 300;
  const Map_Height = 300;
  let Latitudes = [];
  let Longitudes = [];
  let Min_Latitude;
  let Max_Latitude;
  let Min_Longitude;
  let Max_Longitude;
  let Latitude_Range;
  let Longitude_Range;
  let LATITUDE_COORD_RATIO;
  let LONGITUDE_COORD_RATIO;

  $: {
    if (data && data.length > 0) {
      Latitudes = data.map(car => car.lat);
      Longitudes = data.map(car => car.long);
      Min_Latitude = Math.min(...Latitudes);
      Max_Latitude = Math.max(...Latitudes);
      Min_Longitude = Math.min(...Longitudes);
      Max_Longitude = Math.max(...Longitudes);
      Latitude_Range = Max_Latitude - Min_Latitude;
      Longitude_Range = Max_Longitude - Min_Longitude;
      LATITUDE_COORD_RATIO = Map_Height / Latitude_Range;
      LONGITUDE_COORD_RATIO = Map_Width / Longitude_Range;
    }
  }

</script>


<main>
{#if carData && carData.length > 0}
  {#if index > 0}
    <a href={`/details?param1=${encodeURIComponent(callPreviousCar().car_name)}`}>Previous Car</a> 
  {/if}
        
  {#if index < carData.length - 1}
    <a href={`/details?param1=${encodeURIComponent(callNextCar().car_name)}`}>Next Car</a> 
  {/if}
{/if}

<ul><b>Sienna Jeong - KU Leuven - r0881089 </b> </ul>

</main>

<div id="sliderDetails">
  <div class="slider-container">
    <input type="range" min="0" max="20160" bind:value={car} step="1440" id="slider">
    <p id="sliderValue"> </p>
  </div>    
</div>


<style>
.container{
  display: flex;
  align-items: flex-start;
}

/*.gps-data {
/*would it be necessary?
}*/
/*.gps-image{
  width: 300px;
  height: 300px;
}*/

</style>


<div class="container">
  <div class="svg-container">
  <svg width="{Map_Width}" height="{Map_Height}">
    <rect x="0" y="0" width="{Map_Width}" height="{Map_Height}" fill="#efefef" />
    {#each data as car}
      <circle
        cx={(car.long - Min_Longitude) * LONGITUDE_COORD_RATIO}
        cy={(car.lat - Min_Latitude) * LATITUDE_COORD_RATIO}
        r="2"
        opacity={car.car_name == PickCar_Name? '1' : '0.2'}
        fill={CAL_LocationColor(car.type)}
      />
    {/each}
  </svg>
</div>

<div class="gps-data">
  {#each data as item}
    <p>{item.time}: {item.location}</p>
  {/each}
  </div>
</div>

   
