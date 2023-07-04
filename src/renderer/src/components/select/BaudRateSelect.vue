<template>
    <a-modal :footer="null" v-model:visible="visible" title="新增波特率" @ok="handleOk">
      <a-input-search
        placeholder="波特率"
        enter-button="添加"
        v-model:value="newBaudRate"
        @search="addBaudRate"
      />
  
      <a-list
        size="small"
        :pagination="pagination"
        :bordered="true"
        style="margin-top: 5px"
        item-layout="horizontal"
        :data-source="baudRateList"
      >
        <template #renderItem="{ item }">
          <a-list-item>
            <template #actions>
              <a href="#" @click="deleteBaudRate(item)">删除</a>
            </template>
            {{ item.value }}
          </a-list-item>
        </template>
      </a-list>
    </a-modal>
    <a-select
      :defaultValue="defaultBaudRate"
      style="width: 100px"
      @change="selectedChange"
      :options="baudRateList"
    >
      <template #dropdownRender="{ menuNode: menu }">
        <v-nodes :vnodes="menu" />
        <a-divider style="margin: 4px 0" />
        <div
          style="padding: 4px 8px; cursor: pointer"
          @mousedown="(e) => e.preventDefault()"
          @click="visible = true"
        >
          <plus-outlined />
          新增
        </div>
      </template>
    </a-select>
  </template>
  <script lang="ts">
  import { defineComponent, ref, toRefs, onMounted } from "vue";
  import { PlusOutlined } from "@ant-design/icons-vue";
  import { message } from "ant-design-vue";
  
  const number = ["9600", "74880", "115200", "2000000"];
  
  export default defineComponent({
    props: {
      defaultBaudRate: String,
    },
    emits: ["baudRateChange"],
    components: {
      PlusOutlined,
      VNodes: (_, { attrs }) => {
        return attrs.vnodes;
      },
    },
    setup(props, { emit }) {
      const { defaultBaudRate } = toRefs(props);
  
      const visible = ref(false);
      const newBaudRate = ref("");
  
      onMounted(() => {
        emit("baudRateChange", defaultBaudRate.value);
      });
      const selectedChange = (data: string) => {
        emit("baudRateChange", data);
      };
  
      const addBaudRate = () => {
        if (newBaudRate.value.length === 0) {
          message.warning("请输入波特率");
          return;
        }
  
        let baudRate = baudRateList.value.find(
          (x) => x.value === newBaudRate.value
        );
  
        if (baudRate != null) {
          message.warning(`${newBaudRate.value} 已存在`);
          return;
        }
  
        baudRateList.value.push({
          value: newBaudRate.value,
          label: newBaudRate.value,
        });
  
        baudRateList.value.sort((a, b) => parseInt(a.value) - parseInt(b.value));
      };
  
      const handleOk = () => {};
  
      const deleteBaudRate = (item: any) => {
          baudRateList.value = baudRateList.value.filter(
          (x) => x.value !== item.value
        );
      };
  
      const baudRateList = ref(
        number.map((item) => {
          return { value: item, label: item };
        })
      );
  
      const pagination = {
        pageSize: 5,
      };
  
      return {
        pagination,
        deleteBaudRate,
        newBaudRate,
        handleOk,
        visible,
        addBaudRate,
        baudRateList,
        selectedChange,
        defaultBaudRate,
      };
    },
  });
  </script>