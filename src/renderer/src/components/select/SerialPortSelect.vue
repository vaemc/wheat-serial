<template>
  <a-select
    v-model:value="selectedSerialPort"
    @dropdownVisibleChange="selectedFocus"
    @focus="selectedFocus"
    @change="selectedChange"
    style="width: 100px"
  >
    <a-select-option v-for="item in serialPortList" :key="item.value" :value="item.value">
      <a-tooltip placement="left">
        <template #title>{{ item.label }}</template>
        {{ item.value }}
      </a-tooltip>
    </a-select-option>
  </a-select>
</template>
<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue'
import { SerialPort } from "serialport";
async function getSerialPortList() {
  let list = await SerialPort.list()
  return list.map((item) => {
    return { value: item.path, label: (item as any).friendlyName }
  })
}

export default defineComponent({
  setup(_props, { emit }) {
    const selectedSerialPort = ref()
    const serialPortList = ref<any>([])
    const refreshSerialPortList = async (showDefaultPort = false) => {
      let list = (await getSerialPortList()) as any
      serialPortList.value = list
      if (list.length > 0 && showDefaultPort) {
        selectedSerialPort.value = list[0].value
        emit('portChange', selectedSerialPort.value)
      }
    }
    const selectedFocus = () => {
      refreshSerialPortList()
    }
    const selectedChange = (data: string) => {
      emit('portChange', data)
    }
    onMounted(() => {
      refreshSerialPortList(true)
    })
    // onBeforeMount(() => {
    //   refreshSerialPortList(true);
    // });
    return {
      serialPortList,
      selectedSerialPort,
      selectedFocus,
      selectedChange
    }
  }
})
</script>
