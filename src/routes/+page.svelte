<script>
//import {json} from "d3-fetch";
import {onMount} from 'svelte';
import data from '../../src/data/GPSstops.json';
import data2 from '../../src/data/carstops.json';
import data3 from '../../src/data/points_of_interest.json';

let imagePath = 'src/data/nymap.jpg';
let selectData = [];

onMount(async () => {
	const response = await fetch('src/data/GPSstops.json');
	data = await response.json();
});

function handleChange(event) {
	const selectIndex = event.target.selectIndex;
	const selectcar_name = parseInt(event.target.options[selectIndex].value);
	selectData = data.filter((item) => item.car_name === selectcar_name);
}

function getCircleCoords(item) {
	const mapWidth = 0.02;
	const mapLatMax = 51.0784;
	const mapLongMin = 4.593322;
	const x = (item.long - mapLongMin) / mapwidth;
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

	const mapWidth = 600;
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
  const LONGITUDE_TO_PIXEL_RATIO = mapWidth / longitudeRange;

let selectcar_name = ''; // store the selected car ID
  const uniquecar_name = [...new Set(data.map(car => car.car_name))];
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
	<li> Name: <b>Sienna Jeong</b> student number : <b>r0881089</b> University: <b>KU Leuven</b>
</li> <li> <b>overview</b> </li> </ul>

<label for="car_name">Select a car ID:</label>
<select id="car_name" on:change={handleSelect}>
  {#each uniquecar_name as car_name}
    <option value={car_name}>{car_name}</option>
  {/each}
</select>

<svg width=600 height=600>
  <rect x="0" y="0" width="600" height="600" fill="#efefef" />
  {#each data as car}
    <circle
      cx={(car.long - minLongitude) * LONGITUDE_TO_PIXEL_RATIO}
      cy={(maxLatitude - car.lat) * LATITUDE_TO_PIXEL_RATIO}
      r="2"
      opacity="0.2"
      fill={car.car_id === selectcar_name? 'red' : 'black'}
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

</main>


