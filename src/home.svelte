<script>
  import axios from "axios";

  const appid_openweather = "ac0fdd38df429674be21355a4850114b"; //openweathermap appid
  const appid_heremaps = "lmuiUmhDK2JQwBnORnRB"; //here maps appid
  const appcode_heremaps = "W3YBbdzlsKOER9SvMF_V1g"; //here maps app code
  var loading = false; //get loading state varialble
  var city = ""; //store input value varialble1
  var temp = ""; //store temp varialble
  var humidity = ""; //store humidity varialble
  var disc = ""; //store weather discription varialble
  var mapurl = ""; //define varialble for map image url
  var zoomlevel = 14.5; //change zoom level ot the map image , parametert defined in here maps api
  var incomeData =null; //varialble for openweathermap data

  const submitHandler = () => {
    //submit handler function
    loading = true; //chage loading state to true
    console.log(city);

    axios //send a http get request to openweathermap api to fetch data
      .get(
        `http://api.openweathermap.org/data/2.5/weather?q=${city}&APPID=${appid_openweather}&units=metric`
      )
      .then(data => {
        //get data upone a sucsessfull response
        loading = false; //chage loading state to false
        console.log(data.data);
        incomeData = data.data; //assign response data to the variable

        temp = incomeData.main.temp;
        humidity = incomeData.main.humidity;
        disc = incomeData.weather[0].description;
        console.log(temp + " " + humidity + " " + disc);

        mapurl = `https://image.maps.api.here.com/mia/1.6/mapview?app_id=${appid_heremaps}&app_code=${appcode_heremaps}&c=${incomeData.coord.lat},${incomeData.coord.lon}&t=0&z=${zoomlevel}&w=500
&h=500`; //set the url to of the map image
      })
      .catch(err => {
        //handle error
        console.log(err.response);
        loading = false; //chage loading state to false
        window.alert(err.response.data.message); //alert to the user upon a api error
        city = ""; //clear form input
      });
  };
</script>

<style>
  .data {
    text-align: center;
  }

  .maindiv {
    text-align: center;
    margin-top: 5%;
  }

  .tabledata {
    margin-left: 10%;
  }

  .ulwrpper {
    width: 50%;
    text-align: left;
    margin-left: 38%;
  }

  .forminput {
    margin-top: 3%;
  }

  /* loader style */

  .loader {
    margin-left: 45%;
    border: 16px solid #f3f3f3; /* Light grey */
    border-top: 16px solid #3498db; /* Blue */
    border-radius: 50%;
    width: 120px;
    height: 120px;
    animation: spin 2s linear infinite;
  }

  @keyframes spin {
    0% {
      transform: rotate(0deg);
    }
    100% {
      transform: rotate(360deg);
    }
  }
</style>

<div class="maindiv">

  <h1>weather Geo web app</h1>
  {#if loading}
    <div class="loader" />
  {/if}

  <!-- add a html form to handle data -->
  <form class="forminput" on:submit|preventDefault={submitHandler}>

<!-- bind the value with the city variable we declared -->
    <input bind:value={city} placeholder="Enter your city" />

    <button>Fetch Data</button>

  </form>

<!-- svelte supports conditions and logic in markup like pug , hbs view engines -->
  {#if incomeData!==null}
    <div class="data">

      <div class="ulwrpper">

        <table class="tabledata" style="width:40%">

          <tr>
            <td>Tempurature :</td>
            <td>
              <span>{temp}&deg;</span>
            </td>

          </tr>
          <tr>

            <td>Humidity :</td>
            <td>
              <span>{humidity}%</span>
            </td>
          </tr>
          <tr>

            <td>weather like :</td>
            <span>{disc}</span>
          </tr>
        </table>

      </div>

      <img src={mapurl} alt="mapImageView" />
    </div>
  {/if}

</div>
