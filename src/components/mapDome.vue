<template>
  <div class="hello">
    <div class="china_map_box">
      <button @click="returRow()" v-if="code.adcodePro || code.adcodeCity">上一级</button>
      <div id="china_map"></div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'mapDome',
  data () {
    return {
      echarts: null, //echarts 实例
      districtExplorer: null, //DistrictExplorer构建的实例
      adcode: '100000', // 这个adcode是城市（国家省市区）的编号 默认100000 中国
      code: {
        adcodePro: null, //省
        adcodeCity: null //市
      }
    }
  },
  mounted () {
    this.echarts = require('echarts');
    //高德api  构建DistrictExplorer的实例
    window.AMapUI.loadUI(['geo/DistrictExplorer'], DistrictExplorer => {
      /**
       * DistrictExplorer构建的实例
       */
      this.districtExplorer = new DistrictExplorer();
      this.getGeoData(this.adcode);  //根据 adcode 生成地图
    })
  },
  methods: {
    //地图配置 init
    getEchInit (adcode, geoData) {
      let myChartContainer = document.getElementById('china_map');
      let myChartChina = this.echarts.init(myChartContainer);
      // 绘制图表
      let optionMap = {
        series: [
          {
            name: '',
            type: 'map',
            mapType: adcode,
            itemStyle: {
              normal: {
                borderColor: 'rgba(0, 0, 0, 0.2)'
              },
            },
            showLegendSymbol: true,
            label: {
              normal: {
                show: false
              },
            },
            data: []
          }
        ]
      }
      this.echarts.registerMap(adcode, geoData);
      myChartChina.setOption(optionMap);
      myChartChina.on('click', async (params) => {
        let adcode = await this.getadCode(params.name); //通过高德api 获取行政区域 adcode 编号
        this.getGeoData(adcode)
      });
    },
    // 根据 行政区域 adcode 编号 获取相对应的 地图Json数据 进行地图下钻
    getGeoData (adcode) {
      this.districtExplorer.loadAreaNode(adcode, (error, areaNode) => {
        const geoData = {}
        // areaNode对象执行这个方法返回的geoJSON中的features
        geoData.features = areaNode.getSubFeatures()
        if (geoData.features.length != 0) { //最高下钻到市
          this.echarts.init(document.getElementById('china_map')).dispose(); //销毁实例
          this.getEchInit(adcode, geoData)
        }
      })
    },
    //根据地图点击 省份名称获取 行政区域 adcode 编号
    async getadCode (keywords) {
      let adcode = null;
      await axios.get('https://restapi.amap.com/v3/config/district', {
        params: {
          subdistrict: 0,
          keywords: keywords,
          key: 'ff5083ffeef28d1e97be213d453572d6',
        },
      })
        .then((res) => {
          adcode = res.data.districts[0].adcode;
          //返回上一级 操作 做处理
          if (adcode == this.code.adcodeCity) return
          if (this.code.adcodePro == null) { //省
            this.code.adcodePro = adcode;
          } else if (this.code.adcodeCity == null) {//市
            this.code.adcodeCity = adcode;
          }
        })
        .catch((error) => {
          console.log(error);
        });
      return adcode
    },
    //返回上一级操作
    returRow () {
      let adcode = this.adcode; //默认100000
      if (this.code.adcodeCity != null) { //市
        adcode = this.code.adcodePro;
        this.code.adcodeCity = null;
      } else {
        this.code.adcodePro = null;
      }
      this.getGeoData(adcode)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#china_map {
  width: 1200px;
  height: 820px;
}
</style>
