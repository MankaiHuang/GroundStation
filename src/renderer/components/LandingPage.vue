<template>
    <div class="mainCont">
        <div id="map"></div>
        <div class="rightView">
            <div class="title">飞行状态</div>
            <div id="wrapper">
                <canvas id="canvas"></canvas>
            </div>
            <div>
                <div class="processBar">
                    <div class="processBarName">Roll</div>
                    <div class="processBarFull">
                        <div class="processBarValue"
                             :style="{width:(flightGesture.roll/360*100)+'%',backgroundColor:'orange'}"></div>
                    </div>
                </div>
                <div class="processBar">
                    <div class="processBarName">Yaw</div>
                    <div class="processBarFull">
                        <div class="processBarValue"
                             :style="{width:(flightGesture.yaw/360*100)+'%',backgroundColor:'aqua'}"></div>
                    </div>
                </div>
                <div class="processBar">
                    <div class="processBarName">Pitch</div>
                    <div class="processBarFull">
                        <div class="processBarValue"
                             :style="{width:(flightGesture.pitch/360*100)+'%',backgroundColor:'aquamarine '}"></div>
                    </div>
                </div>
            </div>
            <div class="title">气压</div>
            <v-chart class="pressureChart" :options="pressure.option"></v-chart>
            <div class="title">GPS信息</div>
            <div class="gpsInfo">
                <div class="dataTab"><span class="dataTitle">经度:</span><span class="dataValue">{{gpsInfo.latitude}}</span></div>
                <div class="dataTab"><span class="dataTitle">纬度:</span><span class="dataValue">{{gpsInfo.longitude}}</span></div>
                <div class="dataTab"><span class="dataTitle">海拔:</span><span class="dataValue">{{gpsInfo.altitude}}</span></div>
            </div>
        </div>

    </div>

</template>

<script>
    import Model from '../util/model'

    export default {
        name: 'landing-page',
        data() {
            return {
                model: {},
                flightGesture: {
                    roll: 0,
                    yaw: 360,
                    pitch: 0
                },
                pressure: {
                    option: {
                        // Make gradient line here
                        visualMap: [{
                            show: false,
                            type: 'continuous',
                            seriesIndex: 0,
                            min: 0,
                            max: 400
                        }],
                        tooltip: {
                            trigger: 'axis',
                            textStyle:{
                                fontSize:8,
                                color:'#000000'
                            },
                            backgroundColor:'#ffffff',
                        },
                        xAxis: [{
                            type : 'category',
                            data: ['01', '02', '03', '04', '05', '06', '07', '08', '09'],
                            axisLine: {
                                lineStyle: {
                                    color: '#cccccc',
                                    width:1
                                }
                            },
                            axisLabel: {
                                fontSize:8
                            },
                        }],
                        yAxis: [{
                            type : 'value',
                            splitLine: {show: false},
                            axisTick:{
                                show:false
                            },
                            axisLine:{
                                lineStyle:{
                                    color:'#cccccc',
                                    width:0
                                }
                            },
                            axisLabel: {
                                fontSize:8
                            },
                        }],
                        grid: [{
                            top: '5%',
                            bottom: '15%',
                            left:'13%',
                            right:'5%'
                        }],
                        series: [{
                            type: 'line',
                            showSymbol: false,
                            lineStyle:{
                                width:2
                            },
                            data: [10, 200, 20, 300, 400, 11, 60, 80, 30]
                        }]
                    }

                },
                gpsInfo:{
                    longitude:114.1176509857,
                    latitude:22.5316645269,
                    altitude:66.23,
                }
            }
        },
        methods: {
            /**
             * 加载地图
             */
            createMap() {
                if (window.AMap) {
                    var map = new window.AMap.Map('map', {
                        // 是否监控地图容器尺寸变化
                        resizeEnable: true,
                        // 初始化地图层级
                        zoom: 15,
                        mapStyle: 'amap://styles/dark'
                    })
                }
            },
            /**
             * 加载飞行器模型
             */
            loadModel() {
                this.model = new Model(document.getElementById('wrapper'), document.getElementById('canvas'))
                // 模拟飞行器姿势
                setInterval(() => {
                    this.model.rotateBy(this.flightGesture.roll, this.flightGesture.yaw, this.flightGesture.pitch)
                    this.flightGesture.yaw--
                    if (this.flightGesture.yaw <= 0) {
                        this.flightGesture.yaw = 360
                    }
                }, 100)
            }
        },
        mounted() {
            this.loadModel()
            this.createMap()
        },
        created() {
        }
    }
</script>

<style scoped lang="less">
    .mainCont {
        #map {
            width: 100vw;
            height: 100vh;
        }

        .rightView {
            position: absolute;
            right: 2vw;
            top: 2vw;
            background-color: rgba(0, 0, 0, 0.6);
            border-radius: 1vw;

            .title {
                color: #ffffff;
                font-size: 1.8vw;
                line-height: 3vw;
                text-align: center;
            }

            #wrapper {
                width: 18vw;

                #canvas {
                    width: 180px;
                    height: 180px;
                }
            }

            .processBar {
                display: flex;
                height: 1vw;
                width: 90%;
                margin: 0.5vw auto;

                .processBarFull {
                    background-color: #000000;
                    flex: 1;

                    .processBarValue {
                        float: left;
                        height: 100%;
                        border-radius: 0.5vw;
                    }

                    border-radius: 0.5vw;
                }

                .processBarName {
                    text-align: center;
                    width: 3vw;
                    font-size: 1vw;
                    color: #ffffff;
                    line-height: 1vw;
                }
            }

            .pressureChart {
                width: 100%;
                height: 10vw;
            }
            .gpsInfo{
                .dataTab{
                    font-size: 1.5vw;
                    color: #ffffff;
                    text-align: left;
                    font-weight: bold;
                    padding-left: 1vw;
                }
                .dataTitle{
                    display: inline-block;
                    width: 4vw;
                    text-align: right;
                }
                .dataValue{
                    color: aqua;
                    padding-left: 1vw;
                }
            }
        }

    }
</style>
