<template>
  <div id="app">
    <div class="space">
      <div class="planet"></div>
      <div v-for="(item, index) in orbit" :key="index" :style="{ '--width': item+'px'}" class="orbit"></div>
      <div 
        v-for="(item, index) in satelit" 
        class="sat" 
        :id="setId(index)"
        :class="{
          'color-red': isRed(item.color),
          'color-green': isGreen(item.color),
          'color-orange': isOrange(item.color)
        }" 
      ></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  components: {
  },
  data() {
    return {
      data: require('./data/data.json'),
      sat: {
        elt: null,
        a: 0,
        r: 300,
        da: 0.003,
        x: 0,
        y: 0,
        center: { 
          x: (100 - 5), 
          y: (100 - 5)
        }
      },
      max: 600,
      divider: 1
    }
  },
  methods : {
    move(sat) {
      sat.a += sat.da
      sat.x = sat.center.x + (sat.r * Math.sin(sat.a));
      sat.y = sat.center.y + (sat.r * Math.cos(sat.a));
      sat.elt.style.top = sat.y + "px";
      sat.elt.style.left = sat.x + "px";
    },
    setId(index) {
      return 'sat'+index;
    },
    setDuration(radius) {
      const divider = 100/this.max;
      const randomTime = 10 - Math.random()*20;
      return divider*radius + randomTime;
    },
    isRed(color) {
      return color === 'red';
    },
    isOrange(color) {
      return color === 'orange';
    },
    isGreen(color) {
      return color === 'green';
    }
  },
  mounted() {
    const _this = this;
    for (let i=0; i<this.satelit.length; i++) {
      const sat ={ ... this.sat };
      const random = Math.random()*10;
      sat.a = random;
      sat.elt = document.getElementById('sat'+i);
      const radius = this.satelit[i].radius;
      sat.r = radius;
      setInterval(function(){
        _this.move(sat);
      }, _this.setDuration(radius));
    }
  },
  computed: {
    orbit: function () {
      if (this.data.people) {
        const distance  = this.data.people.map(item => item.distance);
        const unique    = [... new Set(distance)];
        const maxDistance = Math.max(...unique);
        const divider   = this.max/maxDistance;
        this.divider    = divider; 
        return unique.map(item => item*divider);
      } else {
        return [];
      }
    },
    satelit: function () {
      if (this.data.people) {
        const arr = this.data.people.map(item => {
          return {
            ...item,
            radius: (item.distance*this.divider)/2
          }
        });
        return arr;
      } else {
        return [];
      }
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 250px;
}
.space {
  position: relative;
  width: 200px;
  height: 200px;
  margin: auto;
}
.space > div {
  position: absolute;
  border-radius: 100%;
}
.planet {
  top: calc((200px - var(--width))/2); 
  right: calc((200px - var(--width))/2);
  --width: 20px;
  width: var(--width);
  height: var(--width);  
  background-color: #ccc;
}
.orbit {
  top: calc((200px - var(--width))/2); 
  right: calc((200px - var(--width))/2);
  --width: 200px;
  width: var(--width); 
  height: var(--width);
  border: solid 1px #ccc;    
}
.sat {
  width: 10px; 
  height: 10px;
  background-color: #cccccc33;
}
.color-red {
  border: solid 1px #B22222;
  background-color: #B2222233;
}
.color-orange {
  border: solid 1px #FF8C00;
  background-color: #FF8C0033;
}
.color-green {
  border: solid 1px #32CD32;
  background-color: #32CD3233;
}
</style>
