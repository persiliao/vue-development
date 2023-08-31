<template>
  <div class="container">
    <div class="title">
      <el-checkbox v-model="checkAll" @change="checkboxClick" :disabled="checkAllDisabled" :indeterminate="isIndeterminate" />
      <el-text>{{ title }}</el-text>
      <el-text class="total">{{ checkedLength }}/{{ treeLength }}</el-text>
    </div>
    <div class="tree-container">
      <el-tree ref="elTreeRef" :data="treeData" :node-key="nodeKey" :props="defaultProps" show-checkbox
               @check="updateCheckedKeys" />
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ElTree } from 'element-plus'
import { computed, onMounted, ref, watch } from 'vue'

const props = defineProps({
  modelValue: [Object],
  title: {
    type: String,
    required: true
  },
  isIndeterminate: {
    type: Boolean,
    default: false
  },
  data: {
    type: Array<any>,
    required: true,
    default: []
  },
  defaultProps: {
    type: Object,
    default: () => {
      return {
        children: 'children',
        label: 'label'
      }
    }
  },
  nodeKey: {
    type: String,
    default: 'id'
  },
  pidKey: {
    type: String,
    default: 'pid'
  }
})

const emits = defineEmits(['update:modelValue'])

const elTreeRef = ref(ElTree)

const treeData = ref<any[]>([])
const treeItems = ref<any[]>([])
const treeLength = computed(() => {
  return treeItems.value.length
})

const checkAll = ref(false)
const checkAllDisabled = computed(() => {
  return treeLength.value === 0
})

const checkedKeys = ref([])
const checkedLength = computed(() => {
  return checkedKeys.value.length
})

const setCheckAll = (checked: boolean) => {
  checkAll.value = checked
}

const addTreeItem = (item: any) => {
  treeItems.value.push(item)
}

const clearTreeItem = () => {
  treeItems.value = []
}

const addTreeItems = (treeArr: any[]) => {
  for (const item of treeArr) {
    addTreeItem(item)
    if (item[props.defaultProps?.children]) {
      addTreeItems(item[props.defaultProps?.children])
    }
  }
}

const updateCheckedKeys = () => {
  checkedKeys.value = elTreeRef.value.getCheckedKeys(false, true)
  emits('update:modelValue', {
    keys: checkedKeys.value,
    nodes: elTreeRef.value.getCheckedNodes(false)
  })
}

const checkboxClick = (checked: boolean) => {
  if (checked) {
    elTreeRef.value.setCheckedNodes(props.data)
  } else {
    elTreeRef.value.setCheckedNodes([])
  }
  updateCheckedKeys()
}

watch(checkedLength, (newCheckedLength) => {
  setCheckAll(treeLength.value === newCheckedLength)
})

watch(() => props.data, (newData) => {
  clearTreeItem()
  newData?.forEach((node) => {
    if (!elTreeRef.value.getNode(node[props.nodeKey])) {
      elTreeRef.value.append(node, node[props.pidKey])
    }
  })
  addTreeItems(treeData.value)
})

onMounted(() => {
  treeData.value = props.data
  addTreeItems(props.data)
})
</script>

<style lang="scss" scoped>

</style>
