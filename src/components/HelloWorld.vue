<template>
  <div class="hello">
    <h1>A Simple Essay for a Humboldt Bus Rapid Transit System</h1>
      <h2>It will help ease the homeless problem</h2>
      <h2>It will help the elderly remain independent</h2>
      <h2>It will be cheaper than expanding roadways</h2>
      <h2>Change is inevitable, even if we change nothing</h2>
      <h1>What will this system actually look like?</h1>
      <GMapMap
      :center="center"
      :zoom="12"
      map-type-id="terrain"
      style="width: 100%; height: 100vh">
      <GMapMarker
      :key="index"
      v-for="(m,index) in busStops"
      :position="m.position"
      :draggable="false"/>
      <GMapMarker
      :key="index"
      v-for="(j,index) in buses"
      :position="j.position"
      :icon='require("../assets/bus.png")'></GmapMarker>
    </GMapMap>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  beforeUnmount() {
    clearInterval(this.interval);
  },
  created(){
    this.date = new Date();
    this.buses.forEach(bus => {
      //very bad but I'll fix it later
      "use strict";
      bus.prevStop = this.busStops[bus.route[((bus.stopIndex-1)%this.busStops.length)].stop];
      console.log((bus.stopIndex-1)%this.busStops.length);
      console.log(this.busStops[((bus.stopIndex-1)%this.busStops.length)]);
      bus.position = JSON.parse(JSON.stringify(bus.prevStop.position));
      bus.currentStop = this.busStops[bus.route[bus.stopIndex].stop];
    })

    //main loop that I wish was data oriented but I don't know how to do that in javascript/vue
    //so its done in forloops, and assumes all data entry is perfect
    //a very bad way to do it, but this ain't unity ecs, I'll remake it for the iOS/Android apps in ecs.
    this.interval = setInterval(()=>{
          this.date = new Date();
        //busbrain
        this.buses.forEach(bus => {
          if (bus.departure.getTime()+bus.route[bus.stopIndex].after<this.date){

            //garbage, should be a pointer but idk how to do that in javascript, or what even is the proper way to do this in javascript? Damn y'all really just hate compiler errors huh. NAN
            //god declared memory managment to be good, and yet we spit in his face, this code is the result of our blasphemy in the face of the one true way (pointers)
            //bus.position = JSON.parse(JSON.stringify(this.busStops[bus.route[bus.stopIndex].stop].position));

            //we've reached the stop, so time to set the stop to the previous stop.
            bus.prevStop = bus.currentStop;


            //change target to the next in the route
            bus.stopIndex = (bus.stopIndex+1)%bus.route.length;

            //set buses travel path to the next point on the route
            bus.currentStop = this.busStops[bus.route[bus.stopIndex].stop];

            //add wait at stops

            //when we'll be departing
            bus.departure= new Date();
          }

          //move the bus
          else {

            //I know I can do this in one line but Idk this just seems cleaner to me, fall back to how I coded in highschool
            //lerp between the start bus stop and the end bus stop for the particular segment of the route
            var amt = (this.date.getTime() - bus.departure.getTime()) / bus.route[bus.stopIndex].after;

            //oh god I miss unity, this would be one function call 😭😿😡
            bus.position.lat=(1-amt)*bus.prevStop.position.lat + amt*bus.currentStop.position.lat;
            bus.position.lng=(1-amt)*bus.prevStop.position.lng + amt*bus.currentStop.position.lng;
          }
        });
    },1225)
  },
  data() {
    return {
      center: {
      lat: 40.86,
      lng: -124.1
    },
    date: null,
    offset: null,
    buses:[
      {
        position: null,
      
      departure: new Date(),
      prevStop:null,
      currentStop:null,
      stopIndex:1,
      route:[        
        {before:50000,stop:1,after:30000},
        {before:40000,stop:2,after:40000},
        {before:40000,stop:1,after:40000},
        {before:50000,stop:3,after:30000},
      ],

      }, {
        position: null,
      
      departure: new Date(),
      prevStop:null,
      currentStop:null,
      stopIndex:1,
      route:[
        {before:40000,stop:2,after:40000},
        {before:40000,stop:1,after:40000},
        {before:50000,stop:3,after:30000},
        {before:50000,stop:1,after:30000},
      ],

      }, {
        position: null,
      
      departure: new Date(),
      prevStop:null,
      currentStop:null,
      stopIndex:1,
      route:[
        {before:50000,stop:3,after:30000},
        {before:40000,stop:2,after:40000},
        {before:50000,stop:1,after:30000},
        {before:40000,stop:2,after:40000},
        {before:40000,stop:1,after:40000},
      ],

      }
    ],
    busStops:[
      {
        position:{
        lat: 40.8,
        lng: -124.2
      },
    },
    {
      position:{
        lat: 40.805217,
        lng: -124.166819
      },
      
    },
    {
      position:{
        lat: 40.793553,
        lng: -124.177508
      },
      
    },
    {
      position:{
        lat: 40.825808,
        lng: -124.176442
      },
      
    },
    {
      position:{
        lat: 40.871864,
        lng: -124.092628
      },
      
    },
    {
      position:{
        lat: 40.884072,
        lng: -124.090361
      },
      
    },
    {
      position:{
        lat: 40.905589,
        lng: -124.081897
      },
    }
    ]
    }
  },
  computed: {
    /* route to compute example
      [        
        {before:50000,stop:1,after:3000},
        {before:40000,stop:2,after:4000},
        {before:40000,stop:1,after:4000},
        {before:50000,stop:3,after:3000},
      ]
      */
  },
  props: {
    msg: String
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
