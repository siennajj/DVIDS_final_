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
    //carData = selectData();
    carIndex = data.findIndex((car) => car.car_name === PickCar_Name);
  });

  function callPreviousCar() {
    if (carIndex > 0) {
      carIndex--;
      PickCar_Name = data[carIndex].car_name;
      return data[carIndex];
    }
  }

  function callNextCar() {
    if (carIndex < data.length - 1) {
      carIndex++;
      PickCar_Name = data[carIndex].car_name;
      return data[carIndex];
    }
  }

function CAL_LocationColor(type) {
    switch(type) {
      case "professional":
        return "orange";
      case "catering":
        return "purple";
      case "domestic":
        return "pink";
      case "housing":
        return "green";
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
  <p>
  <a href={`/details?param1=${encodeURIComponent(callPreviousCar())}`}>Previous Car</a>
  <a href={`/details?param1=${encodeURIComponent(callNextCar())}`}>Next Car</a>
  </p>


  <ul><b style="font-size: 23px;"> Sienna Jeong - KU Leuven - r0881089 </b> </ul>


<div id="sliderDetails">
  <label class="form-label col-sm-10">
  {#if PickCar_Name}
  <p> <b>Details for Car {PickCar_Name}</b></p>
  {/if}
  <input type="range" class="form-range" min="0" max="20160" bind:value={car} step="1440" id="slider"/>
  </label>
</div>   

</main> 

<style>
.container{
  display: flex;
  align-items: flex-start;
}
.timeline-container {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  justify-content: flex-end;
  width: 300px;
  height: 300px;
}
.dayBars {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
}
.day_label {
  margin-right: 10px;
}
.bar {
  position: relative;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: flex-end;
}
.timeline_event {
  position: absolute;
  width: 6px;
  height: 6px;
  border-radius: 50%;
  bottom: 0;
}
.timeline {
  position: absolute;
  width: 1px;
  height: 100%;
  top: 0;
  background-color: rgba(0, 0, 0, 0.2);
}
.timeline_label {
  position: absolute;
  color: rgba(0, 0, 0, 0.5);
  bottom: -20px;

}
.professional {
  background-color: red;
}
.catering {
  background-color: green;
}
.domestic {
  background-color: orange;
}
.housing {
  background-color: purple;
}
</style>


<div class="container">
  <div class="svg-container">
  <svg width="300px" height="300px">
    <rect x="0" y="0" width="300px" height="300px" fill="#efefef" />
  {#each data as car}
    <circle
      cx={(car.long - Min_Longitude) * LONGITUDE_COORD_RATIO}
      cy={(car.lat - Min_Latitude) * LATITUDE_COORD_RATIO}
      r="2"
      opacity="1"
      fill={car.car_id == PickCar_Name ? CAL_LocationColor(location.type) : 'darkgrey'} />

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


<div class="timeline-container">
  {#each Object.keys(customData) as day}
    <div class="dayBars">
      <div class="day_label">{day}</div>
      <div class="bar" style="width: 300px;">

        {#each customData[day] as data}
            <div class="timeline_event {data.type}"></div>
        {/each}

        <div class="timeline" style="left: 0%;"></div>
        <div class="timeline" style="left: 25%;"></div>
        <div class="timeline" style="left: 50%;"></div>
        <div class="timeline" style="left: 75%;"></div>
        <div class="timeline" style="left: 100%;"></div>

        <div class="timeline_label" style="left: -50%; bottom: -20px;"> 0 </div>
        <div class="timeline_label" style="left: -25%; bottom: -20px;"> 6 </div>
        <div class="timeline_label" style="left: 0%; bottom: -20px;"> 12 </div>
        <div class="timeline_label" style="left: 25%; bottom: -20px;"> 18 </div>
        <div class="timeline_label" style="left: 50%; bottom: -20px;" >24 </div>
        
      </div>
    </div>
  {/each}
</div>
