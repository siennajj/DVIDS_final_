<script>
//import {json} from "d3-fetch";
import {onMount} from 'svelte';
import data from '../../src/data/GPSstops.json';
import data2 from '../../src/data/carstops.json';
import data3 from '../../src/data/points_of_interest.json';

let selectData = [];

function handleChange(event) {
	const selectIndex = event.target.selectIndex;
	const selectcar_name = parseInt(event.target.options[selectIndex].value);
	selectData = data.filter((item) => item.car_name === selectcar_name);
}

function getCircleCoords(item) {
	const map_Width = 0.0256;
	const mapLatMax = 50.0784;
	const mapLongMin = 4.78332;
	const x = (item.long - mapLongMin) / map_Width;
	const y = (mapLatMax - item.lat) / mapLatMax;
	const radious = 0.01;
	return '${x},${y},${radius}';
}

function getLocationColor(type) {
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

	const map_Width = 600;
  const mapHeight = 600;
  const latitudes = data.map(car => car.lat);
  const longitudes = data.map(car => car.long);
  const minLatitude = Math.min(...latitudes);
  const maxLatitude = Math.max(...latitudes);
  const minLongitude = Math.min(...longitudes);
  const maxLongitude = Math.max(...longitudes);
  const latitudeRange = maxLatitude - minLatitude;
  const longitudeRange = maxLongitude - minLongitude;
  const LATITUDE_TO_PIXEL_RATIO = mapHeight / latitudeRange;
  const LONGITUDE_TO_PIXEL_RATIO = map_Width / longitudeRange;

// storing the selected car ID
let selectcar_name = ''; 
  const uniquecar_name = [...new Set(data.map(car => car.car_id))];
// Function to handle the selection change
  function handleSelect(event) {
    selectcar_name = event.target.value;
    console.log('Selected car ID:', selectcar_name);
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
      cx={(car.long - minLongitude) * LONGITUDE_TO_PIXEL_RATIO}
      cy={(maxLatitude - car.lat) * LATITUDE_TO_PIXEL_RATIO}
      r="2"
      opacity={car.car_id == selectcar_name? '1' : '0.2'}
      fill={car.car_id == selectcar_name? 'red' : 'black'}
    />
  {/each}
  {#each data3 as location}
    <g class="tooltip">
    <circle
      cx={(location.long - minLongitude) * LONGITUDE_TO_PIXEL_RATIO}
      cy={(maxLatitude - location.lat) * LATITUDE_TO_PIXEL_RATIO}
      r="10"
      fill={getLocationColor(location.type)}
      opacity = "1"
    />
    <title>{location.name}</title>
    </g>
  {/each}
</svg>

<label for="car_name">Select a Car to Highlight:</label>
<select id="car_name" on:change={handleSelect}>
  {#each uniquecar_name as car_name}
    <option value={car_name}>{car_name}</option>
  {/each}
</select>

{#if selectcar_name}
	<p>Go to details for <a href='/src/routes/details/+page.data'>details</a> for {selectcar_name} </p>
{/if}


</main>


