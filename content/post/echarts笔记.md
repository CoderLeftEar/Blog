---
title: "Echarts笔记"
date: 2020-06-16T20:28:21+08:00
categories: ["Echarts"]
tags: ["Echarts"]
author: CoderLeftEar
draft: true
---

# Echarts笔记

官方配置地址

http://echarts.baidu.com/option.html#tooltip.trigger

## eg1:

![](https://img-blog.csdn.net/20171219173215206?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjEzODYyNzU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <!-- 引入 ECharts 文件 -->
    <script src="echarts.js"></script>
	<div id="pie1" style="width: 600px;height:400px;"></div>
	<script type="text/javascript">
    var myChart1 = echarts.init(document.getElementById('pie1'));
    option1 = {
	    title:{
            text:'按类型统计',
            top:'bottom',
            left:'center',
            textStyle:{
                fontSize: 14,
                fontWeight: '',
                color: '#333'
            },
        },//标题
        tooltip: {
            trigger: 'item',
            formatter: "{a} <br/>{b}: {c} ({d}%)"，
            /*formatter:function(val){   //让series 中的文字进行换行
                 console.log(val);//查看val属性，可根据里边属性自定义内容
                 var content = var['name'];
                 return content;//返回可以含有html中标签
             },*/ //自定义鼠标悬浮交互信息提示，鼠标放在饼状图上时触发事件
        },//提示框，鼠标悬浮交互时的信息提示
        legend: {
            show: false,
            orient: 'vertical',
            x: 'left',
            data: ['50%-学生', '25%-老师', '25%-家长']
        },//图例属性，以饼状图为例，用来说明饼状图每个扇区，data与下边series中data相匹配
        graphic:{
            type:'text',
            left:'center',
            top:'center',
            style:{
                text:'用户统计\n'+'100', //使用“+”可以使每行文字居中
                textAlign:'center',
                font:'italic bolder 16px cursive',
                fill:'#000',
                width:30,
                height:30
            }
        },//此例饼状图为圆环中心文字显示属性，这是一个原生图形元素组件，功能很多
        series: [
            {
                name:'用户统计',//tooltip提示框中显示内容
                type: 'pie',//图形类型，如饼状图，柱状图等
                radius: ['35%', '65%'],//饼图的半径，数组的第一项是内半径，第二项是外半径。支持百分比，本例设置成环形图。具体可以看文档或改变其值试一试
                //roseType:'area',是否显示成南丁格尔图，默认false
                itemStyle: {
                    normal:{
                        label:{
                            show:true,
                            textStyle:{color:'#3c4858',fontSize:"18"},
                            formatter:function(val){   //让series 中的文字进行换行
                                return val.name.split("-").join("\n");}
                        },//饼图图形上的文本标签，可用于说明图形的一些数据信息，比如值，名称等。可以与itemStyle属性同级，具体看文档
                        labelLine:{
                            show:true,
                            lineStyle:{color:'#3c4858'}
                        }//线条颜色
                    },//基本样式
                    emphasis: {
                        shadowBlur: 10,
                        shadowOffsetX: 0,
                        shadowColor: 'rgba(0, 0, 0, 0.5)',//鼠标放在区域边框颜色
                        textColor:'#000'
                    }//鼠标放在各个区域的样式
                },
                data: [
                    {value: 50, name: '50%-学生'},
                    {value: 25, name: '25%-老师'},
                    {value: 25, name: '25%-家长'},
                ],//数据，数据中其他属性，查阅文档
                color: ['#51CEC6','#FFB703','#5FA0FA'],//各个区域颜色
            },//数组中一个{}元素，一个图，以此可以做出环形图
        ],//系列列表
    };
    myChart1.setOption(option1);
    </script>
</head>
</html>
```

## eg2:

![](https://img-blog.csdn.net/20171219174458875?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjEzODYyNzU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <!-- 引入 ECharts 文件 -->
    <script src="echarts.js"></script>
	<div id="pie1" style="width: 600px;height:400px;"></div>
	<script type="text/javascript">
    var myChart1 = echarts.init(document.getElementById('pie1'));
    option1 = {
		tooltip: {
			trigger: 'item',
			formatter: "{a} <br/>{b}: {c} ({d}%)"
		},
		series: [
			{
				name:'学段分类统计',
				type: 'pie',
				radius: [0, '35%'],
				label: {
					normal: {
						position: 'inner'
					}
				},//扇形区域内显示文字
				data:[
					{value:25, name:'学生'},
					{value:35, name:'教师'},
					{value:45, name:'家长'}
				],
				color: ['#10EFE0','#EF42A4','#FF8F03']
			},
			{
				name:'角色分类统计',
				type: 'pie',
				radius: ['35%', '75%'],
				labelLine: {
					normal: {
						show: true,
					}
				},
				data:[
					{ value: 10, name: '学生（小学）' },
					{ value: 10, name: '学生（初中）' },
					{ value: 5, name: '学生（高中）' },
					{ value: 15, name: '教师（小学）' },
					{ value: 15, name: '教师（初中）' },
					{ value: 5, name: '教师（高中）' },
					{ value: 20, name: '家长（小学）' },
					{ value: 10, name: '家长（初中）' },
					{ value: 15, name: '家长（高中）' },

				],
				color:['#51CEC6','#6AE2D9','#96F5EF','#EF87C2','#F79DD0','#FFC8E7','#FFB703','#FFCD50','#FFE296']
			},

		],

	};
    myChart1.setOption(option1);
	
	//添加点击事件，可以点击每个区域，其中params为每个区相关属性，可以根据该属性写各种事件
	myChart1.on('click', function(params){
		console.log(params);
	});
    </script>
</head>
</html>
```

