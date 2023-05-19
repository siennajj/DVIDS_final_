<script>
//import {json} from "d3-fetch";
import {onMount} from 'svelte';
import data from '../../src/data/GPSstops.json';
import data2 from '../../src/data/carstops.json';
import data3 from '../../src/data/points_of_interest.json';

let PickData = [];

function dataLink(event) {
	const PickIndex = event.target.PickIndex;
	const PickCar_Name = parseInt(event.target.options[PickIndex].value);
	PickData = data.filter((item) => item.car_name === PickCar_Name);
}

function processCircleCoords(item) {
	const Map_Width = 0.0256;
	const Cir_LatMax = 50.0784;
	const Cir_LongMin = 4.78332;
	const x = (item.long - Cir_LongMin) / Map_Width;
	const y = (Cir_LatMax - item.lat) / Cir_LatMax;
	const radious = 0.01;
	return '${x},${y},${radius}';
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

// storing the selected car ID
let PickCar_Name = ''; 
  const uniquecar_name = [...new Set(data.map(car => car.car_id))];
// Function to handle the selection change
  function UpdateSelection(event) {
    PickCar_Name = event.target.value;
    console.log('Selected car ID:', PickCar_Name);
  }

</script>

<style>
	h1 {
		font-size: 2em;
		color: #333;
		text-align: center;
		margin-top: 2em;
	}

	ul {
		list-style: none;
		padding: 0;
		margin-top: 2em;
	}

	li {
		font-size: 1.2em;
		margin-bottom: 1em;
	}
</style>

<main>
<ul>
	<h1>Data Visualiztion Final Project </h1>
	<li> Name: <b>Sienna Jeong</b> Student Number : <b>r0881089</b> University: <b>KU Leuven</b>
</li> <li> <b>overview</b> </li> </ul>

<svg width=600 height=600>
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

<label for="car_name">Select a Car to Highlight:</label>
<select id="car_name" on:change={UpdateSelection}>
  {#each uniquecar_name as car_name}
    <option value={car_name}>{car_name}</option>
  {/each}
</select>

{#if PickCar_Name}
	<p>Go to details for <a href='/src/routes/details/+page.data'>details</a> for {PickCar_Name} </p>
{/if}


</main>


