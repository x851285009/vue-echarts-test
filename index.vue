<template>
     <div class="peopleRatio">
        <Title title="机构人员数量"></Title>
        <div class="select">
            <el-select v-model="selectValue" @change="seleChange">
                <el-option
                    v-for="item in agentIndex"
                    :key="item.comcode"
                    :label="item.comname"
                    :value="item.comcode"
                    >
                </el-option>
            </el-select>
        </div>
        <section>
            <div class="c-group">
                <div class="gauge" ref="gauge1"></div>
                <div class="gauge" ref="gauge2"></div>
            </div>
            <div class="ratioNum">
                <span class="title">新增人口占比</span>
                <div class="ratio-wapper">
                    <div class="ratio-inner" :style="{width:peopleRatio + '%'}">{{Number(peopleRatio).toFixed(2)}}%</div>
                </div>
            </div>
        </section>
    </div>
</template>

<script>
    import Title  from '../../../components/Title'
    export default {
        data () {
            return {
                agentIndex:[],
                selectValue:'全部',
                peopNum1:0,
                peopNum2:0,
                myGuage1:{},
                myGuage2:{},
                baseUrl:'http://localhost:8088'
            }
        },
        components:{
            Title
        },
        methods : {
            getAgentIndex () {
                let that = this
                this.$axios.post(baseUrl+'/agent/index')
                .then(function (response) {
                    let data = response.data.data
                    data.unshift({
                        "comcode": 0,
                        "comname": "全部"
                    })
                    that.agentIndex = data
                })
                .catch(function (error) {
                    console.log(error)
                });
            },
            getPeopNum (value) {
                let that = this
                let comcode = value*1
                let data = that.$qs.stringify({
                    comcode: comcode
                })
                this.$axios({
                    method: 'post',
                    url: baseUrl+'/agent/getNumTotal',
                    data:data
                })
                .then(function (response) {
                   let data = response.data.data
                   that.peopNum1 = data[0].count
                   that.peopNum2 = data[1].count
                   that.Gauge1()
                   that.Gauge2()
                })
                .catch(function (error) {
                    console.log(error)
                })
            },
            Gauge1 () {
                let that = this
                let dom = that.$refs.gauge1
                let myGuage1 = that.myGuage1
                myGuage1 = this.$echarts.init(dom)
                myGuage1.clear()
                window.onresize = myGuage1.resize
                myGuage1.setOption({
                    series: [
                        {
                            type: 'gauge',
                            radius: '80%',
                            center: ['50%', '50%'],
                            startAngle: 220,
                            endAngle: -40,
                            axisLine: {
                                show: true,
                                lineStyle: {
                                    width: 16,
                                    color: [
                                        [
                                            1, new this.$echarts.graphic.LinearGradient(0, 0, 1, 0, [
                                                {
                                                    offset: 0,
                                                    color: '#ae3df6'
                                                },
                                                {
                                                    offset: 1,
                                                    color: '#53bef9'
                                                }
                                            ])
                                        ],
                                        [
                                            1, '#222e7d'
                                        ]
                                    ]
                                }
                            },
                            splitLine: {
                                show: false,
                            },
                            axisLabel: {
                                show: false
                            },
                            axisTick: {
                                show: false
                            },
                            pointer: {
                                show: false
                            },
                            title: {
                                show: true,
                                offsetCenter: [0, '-15%'], // x, y，单位px
                                textStyle: {
                                    color: '#3b8eed',
                                    fontSize: 26
                                }
                            },
                            detail: {
                                show: true,
                                offsetCenter: [0, 70],
                                color: '#3b8eed',
                                formatter: function(params) {
                                    return params
                                },
                                textStyle: {
                                    fontSize: 28,
                                }
                            },
                            data: [{
                                name:'总人数',
                                value:this.peopNum1
                            }]
                        }
                    ]
                })
            },
            Gauge2 () {
                let that = this
                let dom = that.$refs.gauge2
                let myGuage2 = that.myGuage2
                myGuage2 = this.$echarts.init(dom)
                myGuage2.clear()
                window.onresize = myGuage2.resize
                myGuage2.setOption({
                    series: [
                        {
                            type: 'gauge',
                            radius: '80%',
                            center: ['50%', '50%'],
                            startAngle: 220,
                            endAngle: -40,
                            axisLine: {
                                show: true,
                                lineStyle: {
                                    width: 16,
                                    color: [
                                        [
                                            1, new this.$echarts.graphic.LinearGradient(0, 0, 1, 0, [
                                                {
                                                    offset: 0,
                                                    color: '#ae3df6'
                                                },
                                                {
                                                    offset: 1,
                                                    color: '#53bef9'
                                                }
                                            ])
                                        ],
                                        [
                                            1, '#222e7d'
                                        ]
                                    ]
                                }
                            },
                            splitLine: {
                                show: false,
                            },
                            axisLabel: {
                                show: false
                            },
                            axisTick: {
                                show: false
                            },
                            pointer: {
                                show: false
                            },
                            title: {
                                show: true,
                                offsetCenter: [0, '-15%'], // x, y，单位px
                                textStyle: {
                                    color: '#3b8eed',
                                    fontSize: 26
                                }
                            },
                            detail: {
                                show: true,
                                offsetCenter: [0, 70],
                                color: '#3b8eed',
                                formatter: function(params) {
                                    return params
                                },
                                textStyle: {
                                    fontSize: 28,
                                }
                            },
                            data: [{
                                name:'新增人数',
                                value:this.peopNum2
                            }]
                        }
                    ]
                })
            },
            seleChange (comcode) {
                this.getPeopNum(comcode)
            }
        },
        computed : {
            peopleRatio () {
                let peopleRatio = (this.peopNum2)/(this.peopNum1)*100
                peopleRatio = Number(peopleRatio).toFixed(2)
                peopleRatio = Number(peopleRatio)
                return peopleRatio
            },
        },
        mounted () {
            this.getAgentIndex()
        },
        created () {
            this.getPeopNum(0)
        }
    }
</script>

<style lang="stylus">
    .peopleRatio
        position relative
        .select
            position absolute
            top 10px
            right 10px
        section
            width 100%
            height 350px
            .c-group
                width 100%
                height 240px
                display flex
                justify-content space-around
                .gauge
                    width 45%
                    height 100%
            .ratioNum
                width 90%
                height 100px
                margin 0 auto
                padding-left 50px
                .title
                    line-height 50px
                    font-size 18px
                    color #373737
                    font-weight 500
                .ratio-wapper,.ratio-inner
                    height 24px
                    background #d9e0e8
                    border-radius 12px
                .ratio-wapper
                    width 90%
                    .ratio-inner
                        height 24px
                        background #13a1e2
                        line-height 24px
                        font-size 16px
                        font-weight 600
                        color #ffffff
                        text-align center
</style>