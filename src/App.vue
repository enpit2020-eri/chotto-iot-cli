<template>
  <div id="app">
    <!-- {{ text }} -->
    <div class="sensor-data sensor-data-acc-gyro">
      <div class="sensor-name">加速度ジャイロセンサ</div>
      <div class="data-value">identifier: {{ sensorDataAccGyro.identifier }}</div>
      <div class="data-value">rssi: {{ sensorDataAccGyro.rssi }}</div>
      <div class="data-value">gacc_x: {{ sensorDataAccGyro.gacc_x }}</div>
      <div class="data-value">gacc_y: {{ sensorDataAccGyro.gacc_y }}</div>
      <div class="data-value">gacc_z: {{ sensorDataAccGyro.gacc_z }}</div>
      <div class="data-value">gyro_x: {{ sensorDataAccGyro.gyro_x }}</div>
      <div class="data-value">gyro_y: {{ sensorDataAccGyro.gyro_y }}</div>
      <div class="data-value">gyro_z: {{ sensorDataAccGyro.gyro_z }}</div>
    </div>
    <div class="sensor-data sensor-data-geo-pressure">
      <div class="sensor-name">重力センサ</div>
      <div class="data-value">identifier: {{ sensorDataGeoPressure.identifier }}</div>
      <div class="data-value">rssi: {{ sensorDataGeoPressure.rssi }}</div>
      <div class="data-value">geo_x: {{ sensorDataGeoPressure.geo_x }}</div>
      <div class="data-value">geo_y: {{ sensorDataGeoPressure.geo_y }}</div>
      <div class="data-value">geo_z: {{ sensorDataGeoPressure.geo_z }}</div>
      <div class="data-value">pressure: {{ sensorDataGeoPressure.pressure }}</div>
    </div>
    <div class="sensor-data sensor-data-temp-humid">
      <div class="sensor-name">温度湿度センサ</div>
      <div class="data-value">identifier: {{ sensorDataTempHumid.identifier }}</div>
      <div class="data-value">rssi: {{ sensorDataTempHumid.rssi }}</div>
      <div class="data-value">temperature: {{ sensorDataTempHumid.temperature }}</div>
      <div class="data-value">humid: {{ sensorDataTempHumid.humid }}</div>
    </div>
  </div>
</template>

<script>
import mqtt from "mqtt";
const client = mqtt.connect("ws://127.0.0.1:11883");

const utf8decoder = new TextDecoder(); // default 'utf-8' or 'utf8'

export default {
  mounted() {
    console.log("mounted");

    // this.timerId = setInterval(() => {
    //   this.text += "!";
    // }, 1000);

    client.on("connect", () => {
      console.log("connected");

      client.subscribe("/sensorData");
    });

    client.on("message", (topic, payload) => {
      const message = utf8decoder.decode(payload)
      const data = JSON.parse(message);
      console.log("message:", topic, message)
      this.text = data.identifier;
      if(data.gacc_x){
        // 加速度角速度センサ
        this.sensorDataAccGyro = data
      }else if(data.geo_x){
        // 重力センサ
        this.sensorDataGeoPressure = data
      }else if(data.temperature){
        // 温度・湿度センサ
        this.sensorDataTempHumid = data

      }else{
        this.text="＼(`o`)／"
      }
    })
  },

  beforeDestroy() {
    console.log("beforeDestroy");

    clearInterval(this.timerId);
  },

  data() {
    return {
      text: "hello world!",
      sensorDataAccGyro: {
        identifier: null,
        rssi: 0,
        gacc_x: 0,
        gacc_y: 0,
        gacc_z: 0,
        gyro_x: 0,
        gyro_y: 0,
        gyro_z: 0,
      },
      sensorDataGeoPressure: {
        identifier: null,
        rssi: 0,
        geo_x: 0,
        geo_y: 0,
        geo_z: 0,
        pressure: 0,
      },
      sensorDataTempHumid: {
        identifier: null,
        rssi: 0,
        temperature: 0,
        humid: 0,
      },
      timerId: 0,
    };
  },
};
</script>

<style>
.sensor-data {
  margin: 15px;
  padding: 10px; 
  box-shadow: 0 0px 5px 0 rgba(0, 0, 0, .5);
}
.sensor-name {
  font-size: 26px;
  font-weight: 700;
  color: purple;
  margin: 7px 0;
}
.data-value {
  border-bottom: solid 1px #dddddd;
}
</style>
