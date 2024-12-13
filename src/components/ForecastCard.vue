<template> 
    <div class="weather-card"> 
        <p>{{time}}</p> 
        <h3> {{ Number(tempetature).toFixed(0) }} C</h3> 
    </div> 
</template> 
 
<script> 
 
export default { 
    name: 'WeatherCard', 
    props: ['tempetature', 'description', "time", "icon"], 
 
} 
 
 
</script> 
 
<style scoped> 
 
.weather-card { 
 
    border: 3px solid white; 
    padding: 10px; 
    margin: 10px; 
    width: 200px; 
    height: auto; 
    z-index: 10; 
    overflow: hidden; 
    border-radius: 20px; 
    color: white;
} 
.weather-img { 
    position: relative; 
    z-index: -10; 
} 
.weather-translate-animation { 
    animation-name: translate; 
    animation-duration: 5s; 
    animation-iteration-count: infinite; 
} 
 
@keyframes translate { 
    from { 
        transform: translateX(-150px); 
    } 
    25% { 
        transform: translateX(-50px); 
    } 
    80%{ 
        transform: translateX(80px); 
    } 
    
    to { 
        transform: translateX(150px); 
    } 
} 
 
 
 
 
</style>