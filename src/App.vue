<script setup>

</script>

<template>
<div class="page-container">
<div class="burger-menu-icon"><img src="./assets/icons/burger-menu-icon.jpg" alt=""></div>
<div class="main-display">
  <div class="temprature-slider"><input type="range" min="10" max="40" step="0.5" v-model.number="target_temprature" @mouseup="update_temptrature"></div>
  <div class="temprature-display"><span class="temp-value">{{ current_temprature.toFixed(1) }}<span class="temp-icon">°C</span></span></div>
  <div class="target-temprature-display"> {{ target_temprature.toFixed(1) }} </div>
  <div class="selected-mode"> {{ Capitalise(selected_mode) }} </div>
</div>
<div class="UI-controls-main-container">
  <div class="top-row-UI-controls">
    <div class="scheduler-icon"><img src="./assets/icons/scheduler.jpg" alt="sdl"></div>
    <div class="heat-icon" :class="{greyscale:selected_mode!='heat'}" @click="selected_mode='heat'; update_temptrature();"><img src="./assets/icons/heat.jpg" alt="ht"></div>
    <div class="cool-icon" :class="{greyscale:selected_mode!='cool'}" @click="selected_mode='cool'; update_temptrature();"><img src="./assets/icons/cool.jpg" alt="cl"></div>
    <div class="dehumidifier-icon"><img src="./assets/icons/dehumidifier.jpg" alt="dh"></div>
  </div>
  <div class="bottom-row-UI-controls">
    <div class="fan-icon"><img src="./assets/icons/fan.png" alt="fan"></div>
    <div class="off-icon" :class="{greyscale:selected_mode!='off'}" @click="selected_mode='off'; update_temptrature();"><img src="./assets/icons/off.png" alt="off"></div>
  </div>
</div>
<div class="room-name-display"> {{ Capitalise(room_name)+' Aircon' }} </div>
</div>
</template>

<style scoped>
.greyscale{filter: grayscale(1);}
.page-container{max-width: 300px;position: relative; padding-top: 40px;}
.temprature-slider{text-align: center;}
.burger-menu-icon{position: absolute; top:5px; right: 15px; }
.temprature-display{text-align: center;margin-top: 20px;font-family: sans-serif;font-size: 68px;}
.temp-value{position: relative;}
.temp-icon{position: absolute;top: 9px;right: -25px;font-size: 20px; color: lightcyan;}
.target-temprature-display{text-align: center;margin-top: 8px;font-family: sans-serif;font-size: 24px;}
.selected-mode{color: grey;text-align: center;margin-top: 10px;font-family: sans-serif;font-size: 18px;}
.top-row-UI-controls{display: flex; justify-content: center; gap: 30px;}
.bottom-row-UI-controls{display: flex; justify-content: center; gap: 25px;margin: 5px;}
.room-name-display{text-align: center;margin-top: 8px;font-family: sans-serif;font-size: 20px; color: lightcyan;}
</style>

<script>
export default {
  data(){return{
    room_name:'lounge',
    current_temprature:20,
    current_humidity:null,
    mode:['heat','cool','off'],
    selected_mode:'heat',
    target_temprature:15.5
  }},
  methods:{
    async update_temptrature(){
      console.log('update req')
      const response = await fetch("http://170.64.173.46/api/temp/"+this.room_name, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({ mode:this.selected_mode,target_temp:Number(this.target_temprature)}),
      });
      const data = await response.json();
      console.log(data)
    },
    async get_temprature_list(){
      const response = await fetch("http://170.64.173.46/api/temp", {
      method: "GET"});
      const data = await response.json();
      console.log(data)
      this.selected_mode=data[0].mode;
      this.current_temprature=data[0].current_temp;
      this.room_name=data[0].room;
      this.target_temprature=data[0].target_temp;
    },
    Capitalise(mode_word){return mode_word[0].toUpperCase()+mode_word.slice(1)}
  },
created(){
  this.get_temprature_list();
}
}
</script>
