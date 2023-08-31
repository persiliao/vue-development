<template>
  <el-tree-transfer-pro :data-source="data" />
</template>

<script setup lang="ts">
import ElTreeTransferPro from "@/components/ElTreeTransferPro.vue"
import { ref } from 'vue'

const data = ref([
  {
    id: 1,
    label: 'Level one 1',
    children: [
      {
        id: 4,
        pid: 1,
        label: 'Level two 1-1',
        children: [
          {
            id: 9,
            pid: 4,
            label: 'Level three 1-1-1'
          },
          {
            id: 10,
            pid: 4,
            label: 'Level three 1-1-2'
          }
        ]
      }
    ]
  },
  {
    id: 2,
    label: 'Level one 2',
    children: [
      {
        id: 5,
        pid: 2,
        label: 'Level two 2-1'
      },
      {
        id: 6,
        pid: 2,
        label: 'Level two 2-2'
      }
    ]
  },
  {
    id: 3,
    label: 'Level one 3',
    children: [
      {
        id: 7,
        pid: 3,
        label: 'Level two 3-1'
      },
      {
        id: 8,
        pid: 3,
        label: 'Level two 3-2'
      }
    ]
  }
])
</script>

<style scoped>

</style>
