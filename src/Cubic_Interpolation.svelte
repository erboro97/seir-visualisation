<canvas id="ChartCnvs"  width="3" height="1" on:click|preventDefault={myClick}></canvas>
<!-- https://steemit.com/utopian-io/@loshcat/chart-js-tutorial-1-click-to-add-datapoints -->
<script>

import {afterUpdate} from 'svelte';
import {createEventDispatcher} from 'svelte';
let dispatch = createEventDispatcher();


let globalChartRef;
let avoidDublication=0;
let points = [];
let mySet=[];
let step=10;






function randomColor(alpha) {
    return String('rgba(' + Math.round(Math.random() * 255) + ',' + Math.round(Math.random() * 255) + ',' + Math.round(Math.random() * 255) + ',' + alpha + ')');
}

function click(element, dataAtClick, myChart){
    let scaleRef,
    valueX,
    valueY;
    for (var scaleKey in myChart.scales) {
                    scaleRef = myChart.scales[scaleKey];
                    if (scaleRef.isHorizontal() && scaleKey == 'x-axis-1') {
                        valueX = scaleRef.getValueForPixel(element.offsetX);
                    } else if (scaleKey == 'y-axis-1') {
                        valueY = scaleRef.getValueForPixel(element.offsetY);
                    }
                }
                myChart.data.datasets.forEach((dataset) => {
                    dataset.data.forEach((element) => {
                            if(Math.ceil(valueX / 10) * 10===element.x){
                                element.y=valueY;
                            }

                           
                     })
                      points=dataset.data;
                });
               
                avoidDublication+=1;
                myChart.update();

}

const linearPoints = () =>{
    for (var i=0;i<100;i+=step){
        mySet.push({x:i,y:6});
    }
    return mySet
}

// points=linearPoints();

const myClick = () =>{
   setTimeout(() =>{dispatch('myClick', points);});
}

function createGraph() {
    var config = {
        type: 'scatter',
        labels: ['10','20','30','40','50','60'],
        data: {
            datasets: [{
                label: "Dataset Made of Clicked Points",
                data:  linearPoints(),               
                fill: false,
                showLine: true,
                cubicInterpolationMode: 'monotone'
            }]
        },
        options: {
            onClick: function (element, dataAtClick) {
                click(element,dataAtClick, this);
            },
            title: {
                display: true,
                text: "Chart.js Interactive Points"
            },
            scales: {
                 xAxes: [{
                    type: "linear",
                    display: true,
                    scaleLabel: {
                        display: true,
                        labelString: 'X-Axis'
                    },
                    ticks: {
                        min: 0,
                        suggestedMax: 90
                    }
                }, ],
                yAxes: [{
                    display: true,
                    scaleLabel: {
                        display: true,
                        labelString: 'Y-Axis'
                    },
                    ticks: {
                        beginAtZero: true,
                        suggestedMax: 15
                    }
                }]
            },
            responsive: true,
            maintainAspectRatio: true
        }
    };
    
    config.data.datasets.forEach(function (dataset) {
        dataset.borderColor = randomColor(0.8);
        dataset.backgroundColor = randomColor(0.7);
        dataset.pointBorderColor = randomColor(1);
        dataset.pointBackgroundColor = randomColor(1);
        dataset.pointRadius = 7;
        dataset.pointBorderWidth = 2;
        dataset.pointHoverRadius = 8;
    });
    
    var ctx = document.getElementById('ChartCnvs').getContext('2d');
    globalChartRef = new Chart(ctx, config);    
 
}
afterUpdate(createGraph)
</script>