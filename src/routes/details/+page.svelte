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
  let carIndex = 0;
 
   // Select which data to show
  function selectData() {
    if (PickCar_Name === "all") {
      return data;
    } else {
      return data2.filter((item) => item.car_name === PickCar_Name);
    }
  }

  onMount(() => {
    const urlParams = new URLSearchParams(window.location.search);
    PickCar_Name = urlParams.get('param1');
    //Car_Overview = urlParams.get('param2');
    carData = selectData();
    carIndex = carData.findIndex((car) => car.car_name === PickCar_Name);
  });

  function callPreviousCar() {
    if (carIndex > 0) {
      carIndex--;
      PickCar_Name = carData[carIndex].car_name;
      return carData[carIndex];
    }
    return null;
  }

  function callNextCar() {
    if (carIndex < carData.length - 1) {
      carIndex++;
      PickCar_Name = carData[carIndex].car_name;
      return carData[carIndex];
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
  const Latitudes = data.map((car) => car.lat);
  const Longitudes = data.map((car) => car.long);
  const Min_Latitude = Math.min(...Latitudes);
  const Max_Latitude = Math.max(...Latitudes);
  const Min_Longitude = Math.min(...Longitudes);
  const Max_Longitude = Math.max(...Longitudes);
  const Latitude_Range = Max_Latitude - Min_Latitude;
  const Longitude_Range = Max_Longitude - Min_Longitude;
  const LATITUDE_COORD_RATIO = Map_Height / Latitude_Range;
  const LONGITUDE_COORD_RATIO = Map_Width / Longitude_Range;

  const customData = {};
  for (const item of data) {
    if (item.time) {
      const day = item.time.slice(0, 10);
      if (!customData[day]) {
      customData[day] = [];
    }
    customData[day].push(item);
    }
  }
    
</script>

<main>
  <div class="col-4">
    <button type="button" class="btn btn-primary" on:click={() => {const previousCar = callPreviousCar(); if (previousCar) PickCar_Name = previousCar.car_name;}}>
      Previous Car
    </button>

    <button type="button" class="btn btn-primary" on:click={()  => {const nextCar = callNextCar(); if (nextCar) PickCar_Name = nextCar.car_name;}}>
      Next Car
    </button>
  </div>

  <ul><b style="font-size: 23px;"> Sienna Jeong - KU Leuven - r0881089 </b> </ul>

</main> 

<div id="sliderDetails">
  <label class="form-label col-sm-10">
  {#if PickCar_Name}
  <p> <b>Details for Car {PickCar_Name}</b></p>
  {/if}
  <input type="range" class="form-range" min="0" max="20160" bind:value={car} step="1440" id="slider"/>
  </label>
</div>   


<style>
.container{
  display: flex;
  align-items: flex-start;
}
.svg-container {
  display: flex;
  align-items: flex-start;
  /*margin-right: 10px;*/
}
.svg-data {
  display: flex;
  align-items: flex-start;
  /*margin-top: 10px;*/
}

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
      opacity="1"
      fill={car.car_id == PickCar_Name? 'red' : 'black'}
    />
  {/each}
  
  </svg>
  </div>

 <div class="svg-data">
    {#each data as item}
      {#if item.car_name === PickCar_Name}
      <p>{item.time}: {item.location}</p>
      {/if}
    {/each}
  </div>
</div>

<div style="display: flex; justify-content: flex-end;">   
  <div class="day-bars-container" style="width: 300px; height: 300px;">
    {#each Object.keys(customData) as day}
      <div class="day-bar">
        <div class="day-label">{day}</div>
        <div class="bar" style="width: 300px;">
          {#each customData[day] as data}
              <div class="location-marker {data.type}"></div>
          {/each}
          <div class="time-marker" style="left: 0%;"></div>
          <div class="time-marker" style="left: 25%;"></div>
          <div class="time-marker" style="left: 50%;"></div>
          <div class="time-marker" style="left: 75%;"></div>
          <div class="time-marker" style="left: 100%;"></div>
          {#if day == Object.keys(customData)[Object.keys(customData).length - 1]}
            <div class="time-marker-label" style="left: -50%; bottom: -20px;">0</div>
            <div class="time-marker-label" style="left: -25%; bottom: -20px;">6</div>
            <div class="time-marker-label" style="left: 0%; bottom: -20px;">12</div>
            <div class="time-marker-label" style="left: 25%; bottom: -20px;">18</div>
            <div class="time-marker-label" style="left: 50%; bottom: -20px;">24</div>
          {/if}
        </div>
      </div>
    {/each}
  </div>
</div>
  
