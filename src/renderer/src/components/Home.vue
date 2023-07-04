<template>
    <div class="container">
      <div class="flexible">
        <div id="terminal" style="height: 100vh" class="xterm"></div>
      </div>
      <div class="fixed">
        <div
          style="
            display: flex;
            justify-content: center;
            flex-direction: column;
            padding: 10px;
          "
        >
          <div
            style="
              display: flex;
              justify-content: center;
              flex-direction: row;
              margin-bottom: 5px;
            "
          >
            <div style="width: 60px; text-align: right; align-self: center">
              端口：
            </div>
            <SerialPortSelect @portChange="portChange" />
          </div>
          <div
            style="
              display: flex;
              justify-content: center;
              flex-direction: row;
              margin-bottom: 5px;
            "
          >
            <div style="width: 60px; text-align: right; align-self: center">
              波特率：
            </div>
            <BaudRateSelect
              :defaultBaudRate="defaultBaudRate"
              @baudRateChange="baudRateChange"
            />
          </div>
          <a-button
            @click="openPortBtn()"
            :type="openBtnType"
            style="margin-bottom: 5px"
            >{{ openBtnText }}</a-button
          >
  
          <a-textarea
            v-model:value="content"
            placeholder="请输入内容"
            :auto-size="{ minRows: 4, maxRows: 4 }"
            allow-clear
            :disabled="!isOpen"
          />
  
          <a-row style="margin-bottom: 5px">
            <a-col :span="12" style="display: flex; flex-wrap: nowrap">
              <a-checkbox :disabled="!isOpen" v-model:checked="isSendNewLine"
                >发送新行</a-checkbox
              ></a-col
            >
            <a-col :span="12">
              <a-checkbox v-model:checked="isSendHex" :disabled="!isOpen"
                >发送HEX</a-checkbox
              >
            </a-col>
          </a-row>
  
          <div
            style="display: flex; flex-wrap: wrap; justify-content: flex-start"
          >
            <a-button
              type="primary"
              @click="sendBtn"
              style="margin: 5px; flex: 1"
              :disabled="!isOpen"
            >
              发送
            </a-button>
            <a-button
              type="primary"
              @click="rstBtn"
              style="margin: 5px; flex: 1"
              :disabled="!isOpen"
            >
              重启
            </a-button>
            <a-button
              type="primary"
              @click="clearBtn"
              style="margin: 5px; flex: 1"
            >
              清空
            </a-button>
            <a-button style="margin: 5px; flex: 1" type="primary">
              绘图
            </a-button>
            <a-button type="primary" style="margin: 5px; flex: 1">
              测试1
            </a-button>
          </div>
        </div>
      </div>
    </div>
  </template>
  <script setup lang="ts">
  
  import { onMounted, ref } from "vue";
  import "xterm/css/xterm.css";
  import "xterm/lib/xterm.js";
  import { Terminal } from "xterm";
  import { FitAddon } from "xterm-addon-fit";
  import SerialPortSelect from "./select/SerialPortSelect.vue";
  import BaudRateSelect from "./select/BaudRateSelect.vue";
  
  const selectedSerialPort = ref<string>();
  const selectedbaudRate = ref<string>();
  const openBtnText = ref("打开端口");
  const openBtnType = ref("primary");
  
  const isOpen = ref(false);
  
  const content = ref();
  
  const isSendNewLine = ref(false);
  const isSendHex = ref(false);
  
  const defaultBaudRate = ref("115200");
  
  const portChange = (data: string) => {
    selectedSerialPort.value = data;
  };
  const baudRateChange = (data: string) => {
    selectedbaudRate.value = data;
  };
  
  
  const openPortBtn = () => {};
  const sendBtn = () => {};
  const clearBtn = () => {};
  const rstBtn = () => {};
  const fitAddon = new FitAddon();
  
  const terminal = new Terminal({
    fontSize: 14,
    allowProposedApi: true,
    cursorStyle: "bar",
    theme: {
      background: "#000000",
      magenta: "#e39ef7",
    },
  });
  
  window.onresize = () => {
    for (let index = 0; index < 100; index++) {
      fitAddon.fit();
    }
  };
  
  onMounted(() => {
    terminal.loadAddon(fitAddon);
    terminal.open(document.getElementById("terminal") as HTMLElement);
    fitAddon.fit();
  
  //   logo.split("\n").forEach((line) => {
  //     terminal.writeln(line);
  //   });
  });
  </script>
  <style>
  .container {
    display: flex;
  }
  
  .flexible {
    flex-grow: 1;
  }
  
  .fixed {
    width: 200px;
  }
  </style>