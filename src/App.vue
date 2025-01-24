<template >
  <div class="flex flex-col items-center " ><!--  :class="{'bg-slate-200': times<sunrise || times>sunset, 'bg-gray-900': times>sunrise || times<sunset} -->
    <div v-if="loading" class="loader">Loading...</div>
    <div class="flex items-center gap-2 my-5">
      <img src="../img/sun.png" alt="Sun Icon" height="70px" width="70px"/>
      <h1 class="text-4xl font-bold">Weather - Meteo</h1>  
      <img src="../img/rain.png" alt="Sun Icon" height="70px" width="70px"/>
    </div>
    
    <div>
      <label class="hidden"></label>
      <input type="text" class="searchInput relative w-[15em] shadow border rounded-[40px] pl-[30px] py-1 " aria-label="Search Location" placeholder="Location" v-model="location" />
      <button class="border ml-3 py-1 px-3 rounded-[40px] bg-blue-500 hover:bg-blue-700 text-white font-bold" @click="getWeather">Search</button>
    </div>
        
    <div v-if="weather" class="flex mt-14 ">
      
      <div class="flex flex-col text-center justify-center gap-5 shadow-[10px_6px_20px_-5px_#3030308f] w-[23em] border mr-12 py-6 px-4 bg-[#ebfcff] rounded-[30px]" >
        <p class="text-3xl font-bold">{{ location }} {{ country }}</p>
        <h2 class="text-5xl font-bold">{{ timestampToTime(times) }}</h2>
        <p>{{ timestampToDay(times) }}</p>
      </div>

      <div class="grid grid-cols-4 grid-rows-2 gap-2 shadow-[10px_6px_20px_-5px_#3030308f] border mr-12 py-6 px-4 bg-[#ebfcff] rounded-[30px]">
          <div class="flex flex-col justify-center">
             <h3 class="text-center text-5xl font-bold"> {{ tempActual }}ºC</h3>
             <p>Feels like : <span class=" text-center text-xl font-bold">{{ feels_like }}ºC</span></p>
          </div>

          <div class="col-span-2 flex justify-center">
            <img :src="`https://openweathermap.org/img/wn/${this.climatIcon}@2x.png`" alt="Climat Icon" width="150px" />
          </div>

          <div class="col-start-4 flex">
            <div class="flex flex-col justify-center items-center gap-2 mr-2">
              <img src="../img/humidity.png" alt="Humidity Icon" width="25px"/>
              <p class="font-bold">{{ humidity }}%</p>
              <p class="text-xs">Humidity</p>
            </div>
            <div class="flex flex-col  justify-center items-center gap-2 ml-2">
              <img src="../img/wind.png" alt="Wind Speed Icon" width="22px"/>
              <p class="font-bold">{{ windSpeed }}km/h</p>
              <p class="text-xs">Wind Speed</p>
            </div>
          </div>

          <div class="row-start-2">
            <div class="flex text-center">
              <img src="../img/sunrise-white.png" alt="Sunrise Icon" width="auto"/>
              <div class="flex flex-col ml-2">
                 <p class="font-bold">Sunrise</p>
                <p>{{ timestampToTime(sunrise) }}</p>
              </div>
            </div>
            <div class="flex text-center mt-4">
              <img src="../img/sunset-white.png" alt="Sunset Icon" width="auto"/>
              <div class="flex flex-col ml-2">
                 <p class="font-bold">Sunset</p>
                <p>{{ timestampToTime(sunset) }}</p>
              </div>
            </div>
          </div>

          <div class="col-span-2 row-start-2 text-center flex flex-col justify-center">
            <p class="text-2xl font-bold mb-7">{{ capitalizeFirstLetter(climatDescription) }}</p>
            <p>Temp. Mini: <span class="font-bold">{{ tempMini }}ºC</span></p>
            <p>Temp. Max: <span class="font-bold">{{ tempMax }}ºC</span></p>
          </div>

          <div class="col-start-4 row-start-2 ">
             <div class="flex flex-col justify-center items-center gap-2">
              <img src="../img/pressure-white.png" alt="Pressure Icon" width="25px"/>
              <p class="font-bold">{{ pressure }}hPa</p>
              <p class="text-xs">Pressure</p>
            </div>
          </div>
      </div>
    
    </div>
    
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      loading: false,
      location: "",
      country: "",
      times:null,
      weather: null,
      main: "",
      tempActual : null,
      tempMini : null,
      tempMax: null,
      climatDescription: null,
      climatIcon: null,
      sunrise : null,
      sunset : null,
      windSpeed:null,
      humidity: null,
      pressure:null,
      feels_like:null,
      linkIcon : null,
      error: "",
    };
  },
  methods: {
    getWeather() {
      this.loading = true;
      // Initializa de nuevo estas variables
      this.weather = null;
      this.error = "";
      // call Open Weather API
      fetch(`https://api.openweathermap.org/data/2.5/weather?q=${this.location}&appid=${import.meta.env.VITE_APP_APIKEY}&units=metric`)
      .then(response => {
        console.log(response);
        if(!response.ok) throw new Error("Probleme search datas"); // Permite generar un error si no encuentro datos de fetch
        return response.json();
      })
      .then(data => {
        console.log(data)
        this.weather = data;
        this.location = data.name;
        this.times = data.dt;
        this.country = data.sys.country;
        this.sunrise = data.sys.sunrise;
        this.sunset = data.sys.sunset;
        this.tempActual= Math.floor(data.main.temp);
        this.tempMax = Math.floor(data.main.temp_max);
        this.tempMini = Math.floor(data.main.temp_min);
        this.humidity = data.main.humidity;
        this.pressure = data.main.pressure;
        this.feels_like = Math.floor(data.main.feels_like);
        this.climatDescription = (data.weather[0].description);
        this.climatIcon = data.weather[0].icon;
        this.windSpeed = Math.floor(data.wind.speed);
        this.loading = false;
        
      })
      .catch(error => {
        console.log(error)
        this.loading = false;
      })
      .finally(() => {
        this.loading = false;
      })
    },
    timestampToTime(timestamp) {
      const date = new Date(timestamp * 1000);
      const hours = date.getHours();
      const minutes = date.getMinutes();
      let time = `${hours}:${minutes}`;
      return time;
    },
    timestampToDay(timestamp) {
      const date = new Date(timestamp * 1000);
      const dayWeekIndex = date.getDay();
      const nameDay = ["Lunes", "Martes", "Miércoles", "Jueves", "Viernes", "Sábado", "Domingo"]; 
      const dayWeek = nameDay[dayWeekIndex]
      const dayMonth = date.getDate(); 
      const monthIndex = date.getMonth() ;
      const nameMonths = ["Enero", "Febrero", "Marzo", "Abril", "Mayo", "Junio",
                      "Julio", "Agosto", "Septiembre", "Octubre", "Noviembre", "Diciembre"];
      const month = nameMonths[monthIndex];
      let day = `${dayWeek}, ${dayMonth} ${month}`;
      return day;
    },
    capitalizeFirstLetter(myWord) {
  return myWord.charAt(0).toUpperCase() + myWord.slice(1);
}
  },
};
</script>

<style>
body{
  background-color: aliceblue;
}

input[type="text"]::before {
  content: "";
  position: absolute;
  top: 10px;
  left: 10px;
  width: 20px;
  height: 20px;
  background-image: url('../img/search.png'); 
  background-size: cover;
  background-repeat: no-repeat;
  pointer-events: none;

}

/* Style of Spinner Loading */
.loader {
  color: #3b82f6;
  font-size: 90px;
  text-indent: -9999em;
  overflow: hidden;
  width: 1em;
  height: 1em;
  border-radius: 50%;
  margin: 72px auto;
  position: relative;
  -webkit-transform: translateZ(0);
  -ms-transform: translateZ(0);
  transform: translateZ(0);
  -webkit-animation: load6 1.7s infinite ease, round 1.7s infinite ease;
  animation: load6 1.7s infinite ease, round 1.7s infinite ease;
}
@-webkit-keyframes load6 {
  0% {
    box-shadow: 0 -0.83em 0 -0.4em, 0 -0.83em 0 -0.42em, 0 -0.83em 0 -0.44em, 0 -0.83em 0 -0.46em, 0 -0.83em 0 -0.477em;
  }
  5%,
  95% {
    box-shadow: 0 -0.83em 0 -0.4em, 0 -0.83em 0 -0.42em, 0 -0.83em 0 -0.44em, 0 -0.83em 0 -0.46em, 0 -0.83em 0 -0.477em;
  }
  10%,
  59% {
    box-shadow: 0 -0.83em 0 -0.4em, -0.087em -0.825em 0 -0.42em, -0.173em -0.812em 0 -0.44em, -0.256em -0.789em 0 -0.46em, -0.297em -0.775em 0 -0.477em;
  }
  20% {
    box-shadow: 0 -0.83em 0 -0.4em, -0.338em -0.758em 0 -0.42em, -0.555em -0.617em 0 -0.44em, -0.671em -0.488em 0 -0.46em, -0.749em -0.34em 0 -0.477em;
  }
  38% {
    box-shadow: 0 -0.83em 0 -0.4em, -0.377em -0.74em 0 -0.42em, -0.645em -0.522em 0 -0.44em, -0.775em -0.297em 0 -0.46em, -0.82em -0.09em 0 -0.477em;
  }
  100% {
    box-shadow: 0 -0.83em 0 -0.4em, 0 -0.83em 0 -0.42em, 0 -0.83em 0 -0.44em, 0 -0.83em 0 -0.46em, 0 -0.83em 0 -0.477em;
  }
}
@keyframes load6 {
  0% {
    box-shadow: 0 -0.83em 0 -0.4em, 0 -0.83em 0 -0.42em, 0 -0.83em 0 -0.44em, 0 -0.83em 0 -0.46em, 0 -0.83em 0 -0.477em;
  }
  5%,
  95% {
    box-shadow: 0 -0.83em 0 -0.4em, 0 -0.83em 0 -0.42em, 0 -0.83em 0 -0.44em, 0 -0.83em 0 -0.46em, 0 -0.83em 0 -0.477em;
  }
  10%,
  59% {
    box-shadow: 0 -0.83em 0 -0.4em, -0.087em -0.825em 0 -0.42em, -0.173em -0.812em 0 -0.44em, -0.256em -0.789em 0 -0.46em, -0.297em -0.775em 0 -0.477em;
  }
  20% {
    box-shadow: 0 -0.83em 0 -0.4em, -0.338em -0.758em 0 -0.42em, -0.555em -0.617em 0 -0.44em, -0.671em -0.488em 0 -0.46em, -0.749em -0.34em 0 -0.477em;
  }
  38% {
    box-shadow: 0 -0.83em 0 -0.4em, -0.377em -0.74em 0 -0.42em, -0.645em -0.522em 0 -0.44em, -0.775em -0.297em 0 -0.46em, -0.82em -0.09em 0 -0.477em;
  }
  100% {
    box-shadow: 0 -0.83em 0 -0.4em, 0 -0.83em 0 -0.42em, 0 -0.83em 0 -0.44em, 0 -0.83em 0 -0.46em, 0 -0.83em 0 -0.477em;
  }
}
@-webkit-keyframes round {
  0% {
    -webkit-transform: rotate(0deg);
    transform: rotate(0deg);
  }
  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}
@keyframes round {
  0% {
    -webkit-transform: rotate(0deg);
    transform: rotate(0deg);
  }
  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}


</style>
