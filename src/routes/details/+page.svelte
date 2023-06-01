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
    carData = select(data2, PickCar_Name);
  });

  // Select which data to show
  function selectData(data, data2, data3) {
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

 // function 

</script>

<style>
.container{
  display: flex;
  align-items: flex-start;
}

.gps-data {
/*would it be necessary?*/
}
.gps-image{
  width: 300px;
  height: 300px;
}

</style>


<div class="container">
  <div class="svg-container">
  <svg width="300" height="300">
    <rect x="0" y="0" width="600" height="600" fill="#efefef" />
  {#each data as car}
    <circle
      cx={(car.long - Min_Longitude) * LONGITUDE_COORD_RATIO}
      cy={(Max_Latitude - car.lat) * LATITUDE_COORD_RATIO}
      r="2"
      opacity={car.car_id == PickCar_Name? '1' : '0.2'}
      fill={car.car_id == PickCar_Name? 'red' : 'black'}
    />
  {/each}
  {#each data3 as location}
    <g class="tooltip">
    <circle
      cx={(location.long - Min_Longitude) * LONGITUDE_COORD_RATIO}
      cy={(Max_Latitude - location.lat) * LATITUDE_COORD_RATIO}
      r="10"
      fill={CAL_LocationColor(location.type)}
      opacity = "1"
    />
    <title>{location.name}</title>
    </g>
  {/each}
  </svg>
</div>

<div class="gps-data">
  {#each carData as item}
    <p>{item.time}: {item.location}</p>
  {/each}
  </div>
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
    <p id="sliderValue">{car}</p>
  </div>    
</div>