<template>
  <div>
    <div class="requests">
      <button type="button" name="button" v-on:click="fillContainer()">FillContainer</button>
      <button type="button" name="button" v-on:click="getSolution()">GetSolution</button>
      <button type="button" name="button" v-on:click="selectBox()">Select</button>
    </div>
    <a-scene>
      <a-camera position="10 10 20" rotation="0 0 0"></a-camera>

      <vue-box :ref="box.ID" v-for="(box, i) in boxes" :key="i" :data="box"></vue-box>

      <a-plane position="0 0 0" rotation="0 0 0" width="100" height="100" color="#7BC8A4"></a-plane>
    </a-scene>
  </div>
</template>

<script>
import VueBox from '@/components/shared/VueBox'
import axios from 'axios'

export default {
  name: 'HelloWorld',
  components: {
    VueBox
  },
  data () {
    return {
      boxes: [
        {
          position: '0 0 0',
          W: 1,
          H: 1,
          color: '#FF0000'
        }
      ],
      allBoxes: [],
      testReq: {
        ContainerID: '1',
        Boxes: []
      },
      testReqSolution: {
        ContainerIDs: [
          '1'
        ],
        Boxes: [],
        Rotation: false
      }
    }
  },
  methods: {
    selectBox (event) {
      console.log(this.$refs.A)
    },
    getBoxes () {
      axios.get('http://52.157.147.48:80/PackingAPI/api/v1/GetBoxes').then((response) => {
        this.allBoxes = response.data
      }).catch((error) => {
        console.log(error)
      })
    },
    fillContainer () {
      this.testReq.Boxes = this.allBoxes.map(box => {
        return {
          BoxID: box.ID,
          Rotated: false
        }
      })
      axios.post('http://52.157.147.48:80/PackingAPI/api/v1/FillContainer', this.testReq).then((response) => {
        this.boxes = this.prepareBoxes(response.data.PackedBoxes)
      }).catch((error) => {
        console.log(error)
      })
    },
    getSolution () {
      this.testReqSolution.Boxes = this.allBoxes.map(box => {
        return {
          BoxID: box.ID,
          Rotated: false
        }
      })
      axios.post('http://52.157.147.48:80/PackingAPI/api/v1/GetSolution', this.testReqSolution).then((response) => {
        this.boxes = this.prepareBoxes(response.data[0].PackedBoxes)
      }).catch((error) => {
        console.log(error)
      })
    },
    prepareBoxes (data) {
      return data.map(box => {
        box.W /= 10
        box.H /= 10
        Object.defineProperties(box, {
          'position': {
            enumerable: true,
            configurable: true,
            writable: true,
            value: `${box.X / 10} ${box.Y / 10} 0`
          },
          'color': {
            enumerable: true,
            configurable: true,
            writable: true,
            value: `#${Math.random().toString(16).slice(2, 8)}`
          }
        })
        return box
      })
    }
  },
  created () {
    this.getBoxes()
  }
}
</script>

<style scoped>
.requests {
  position: absolute;
  top: 0;
  left: 0;
  z-index: 10;
}
</style>
