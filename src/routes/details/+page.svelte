<script>
  import {onMount} from 'svelte';
  import data from '/src/data/GPSstops.json';
  import data2 from '/src/data/carstops.json';
  import data3 from '/src/data/points_of_interest.json';

// State
  let car = 0;
  let PickCar_Name = "";
  let Car_Overview = "";
  let carData = [];

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
</script>


<div container>

  <div class="day-bars-container" style="width: 300px; height: 300px;">
    {#each Object.keys(groupedData) as day}
      <div class="day-bar">
        <div class="day-label">{day}</div>
        <div class="bar" style="width: 300px;">
          {#each groupedData[day] as data}
              <div class="location-marker {data.type}"></div>
          {/each}
          <div class="time-marker" style="left: 0%;"></div>
          <div class="time-marker" style="left: 25%;"></div>
          <div class="time-marker" style="left: 50%;"></div>
          <div class="time-marker" style="left: 75%;"></div>
          <div class="time-marker" style="left: 100%;"></div>
          {#if day == Object.keys(groupedData)[Object.keys(groupedData).length - 1]}
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


<main>
  <p>
    <a href="/overview">Car Overview</a> 
    {#if carData && carData.length > 0}
      {#each carData as car, index}
        {#if car === PickCar_Name}
          {#if index > 0}
            <a href={`/details?param1=${encodeURIComponent(carData[index - 1])}`}>Previous Car</a> 
          {/if}
          {#if index < carData.length - 1}
            <a href={`/details?param1=${encodeURIComponent(carData[index + 1])}`}>Next Car</a> 
          {/if}
        {/if}
      {/each}
    {/if}
  </p>

<ul><b>Sienna Jeong - KU Leuven - r0881089 </b> </ul>

</main>
   
<div id="sliderDetails">
    <input type="range" min="0" max="20160" bind:value={car} step="1440" id="slider">
    <p id="sliderValue">{car}</p>
    
</div>

 




