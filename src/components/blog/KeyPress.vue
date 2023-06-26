<script setup>
import { reactive } from 'vue'
import { useEventListener } from '@vueuse/core'

const press = reactive({
  key: 0,
  keyCode: 0,
})

const down = reactive({
  key: 0,
  keyCode: 0,
})

useEventListener('keydown', (e) => {
  down.key = e.key
  down.keyCode = e.keyCode
})

useEventListener('keypress', (e) => {
  press.key = e.key
  press.keyCode = e.keyCode
})

</script>

<template>
  <table border>
    <thead>
      <tr>
        <th>type</th>
        <th>key</th>
        <th>keyCode</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>keypress</td>
        <td>{{ press.key }}</td>
        <td>{{ press.keyCode }}</td>
      </tr>
      <tr>
        <td>keydown</td>
        <td>{{ down.key }}</td>
        <td>{{ down.keyCode }}</td>
      </tr>
    </tbody>
  </table>
</template>
