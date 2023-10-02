<script setup>
import { ref } from 'vue'
import SocketConfig from './components/SocketConfig.vue'
import ChromaticTable from './components/ChromaticTable.vue'
import JewellerTable from './components/JewellerTable.vue'

function RGB(red, green, blue) {
    this.red = red,
    this.green = green,
    this.blue = blue
}

RGB.prototype.totalValue = function() {
  return this.red + this.green + this.blue
}

RGB.prototype.nonZero = function() {
  return (this.red > 0 ? 1 : 0) + (this.green > 0 ? 1 : 0) + (this.blue > 0 ? 1 : 0)
}

function ChromaticMethod(cost, rgb, name) {
    this.cost = cost,
    this.guaranteedColors = rgb,
    this.name = name
}

const chromaticMethods = [
  new ChromaticMethod(1, new RGB(0,0,0), "Chromatic Orb"),
  new ChromaticMethod(4, new RGB(1,0,0)),
  new ChromaticMethod(4, new RGB(0,1,0)),
  new ChromaticMethod(4, new RGB(0,0,1))
]
  
const sockets = ref(4)

const requirements = ref(new RGB(50, 0, 0))

const desiredSockets = ref(new RGB(2, 1, 1))

const totalreq = ref(0)

const chromaticData = ref([
  {
    "method": "Chromatics",
    "success": "100"
  }
])

const jewellerData = ref([
  {
    "method": "Jewellers",
    "success": "100"
  }
])


function updateTable() {
  let newChromaticData = []
  for (const method of chromaticMethods) {
    newChromaticData.push(calcChromaticChance(method, sockets.value, requirements.value, desiredSockets.value))
  }

  chromaticData.value = newChromaticData
}

/*
Given a bench crafting method using chromatics, calculate the chance of hitting
a desired combination of socket colors.

!Note! Chromatic orbs cannot roll the same permutation twice in a row (Bench craft still can)
1. Subtract the guaranteed sockets from bench craft from the desired sockets
2. Calculate chances of rolling each color given requirements
3. Calculate chance of permutation of colors
4. Multiply chance of permutation by amount of possible permutations w/o duplicates: n! / r!*g!*b!
where n = number of sockets, and r g b = amount of desired color. Exclude if 0.
5. For chromatic orb method, divide resulting chance by 1 - the total chance of all other combinations happening twice in a row
*/
function calcChromaticChance(method, sockets, requirements, desiredSockets) {
  console.log("Total value: " + desiredSockets.totalValue())
  if (desiredSockets.totalValue() > sockets)
    return

  console.log(requirements)
  const socketChances = calcSocketChances(requirements)
  console.log(socketChances)
  const adjustedDesiredSockets = new RGB(desiredSockets.red - method.guaranteedColors.red, desiredSockets.green - method.guaranteedColors.green, desiredSockets.blue - method.guaranteedColors.blue)
  console.log(adjustedDesiredSockets)
  const chance = calcCombinationProbability(sockets, socketChances, adjustedDesiredSockets)

  console.log("chance: " + chance)
  return {
    method: method.name,
    success: (chance * 100) + "%"
  }
}

function calcSocketChances(requirements) {
  if (requirements.nonZero() == 1) {
    console.log("Mono requirement!")
    return calcMonoColorProbabilities(requirements)
  }

  else
    return new RGB(0,0,0)
}

/*

*/
function calcMonoColorProbabilities(requirements) {
  const r = Math.max(requirements.red, requirements.green, requirements.blue)
  const onColor = 0.9 * (r + 10) / (r + 20)
  const offColor = 0.05 + 4.5 / (r + 20)
  return new RGB(requirements.red > 0 ? onColor : offColor, requirements.green > 0 ? onColor : offColor, requirements.blue > 0 ? onColor : offColor)
}

/*function calcDuoColorProbaDuolities(requirements) {
  const r1, r2 = 
  const onColor1 = 0.9 * r1 / (r1 + r2)
  const onColor2 = 0.9 * r2 / (r1 + r2)
  const offColor = 0.1
}*/

/*function calcTrioColorProbabilities(requirements) {

}*/

function calcCombinationProbability(sockets, socketChances, desiredSockets) {
  const actualSockets = desiredSockets.totalValue()
  const probability = Math.pow(socketChances.red, desiredSockets.red)
    * Math.pow(socketChances.green, desiredSockets.green)
    * Math.pow(socketChances.blue, desiredSockets.blue)
  
  console.log("Probability: " + probability)
  
  console.log("Sockets: " + sockets)
  console.log(desiredSockets)

  return probability * factorial(actualSockets) / factorial(desiredSockets.red) * factorial(desiredSockets.green) * factorial(desiredSockets.blue)
}

function factorial(x) {
  if (x <= 0)
    return 1

  let product = x
  console.log("Init product: " + product)
  while (x > 1) {
    x--
    product = product * x
  }

  console.log("Product: " + product)
  return product
}

function calcTotalReq() {
  totalreq.value = requirements.value.red + requirements.value.green + requirements.value.blue
}
</script>

<template>
  <header>
    <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="125" height="125" />
    <h3>Path of Exile Color Cost Calculator</h3>
  </header>

  <main>
    <div>
      <input type="number" v-model="sockets" placeholder="Sockets" min="1" max="6">
    </div>
    <div>
      <input type="number" v-model="requirements.red" placeholder="Str">
      <input type="number" v-model="requirements.green" placeholder="Dex">
      <input type="number" v-model="requirements.blue" placeholder="Int">
    </div>
    <div>
      <input v-model="desiredSockets.red" placeholder="R">
      <input v-model="desiredSockets.green" placeholder="G">
      <input v-model="desiredSockets.blue" placeholder="B">
    </div>
    <button @click="updateTable">Calculate</button>
    <div>
    <table>
      <thead>
        <tr>
          <th>Craft Type</th>
          <th>Success Chance</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in chromaticData">
          <td>{{ item.method }}</td>
          <td>{{ item.success }}</td>
        </tr>
      </tbody>
    </table>
  </div>
  <div>
    <table>
      <thead>
        <tr>
          <th>Craft Type</th>
          <th>Success Chance</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in jewellerData">
          <td>{{ item.method }}</td>
          <td>{{ item.success }}</td>
        </tr>
      </tbody>
    </table>
  </div>

  <div>
    <span>Total req: {{ totalreq }}</span>
  </div>
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
