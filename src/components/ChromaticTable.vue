<script setup>
const data = [
  {
    "method": "Chromatics",
    "success": "100"
  }
]
</script>

<template>
  <div>
    <table>
      <thead>
        <tr>
          <th>Craft Type</th>
          <th>Success Chance</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in data">
          <td>{{ item.method }}</td>
          <td>{{ item.success }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style>
