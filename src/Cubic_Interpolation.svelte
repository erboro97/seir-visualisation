<canvas id="ChartCnvs"  width="3" height="1" on:click|preventDefault={myClick}></canvas>
<!-- https://steemit.com/utopian-io/@loshcat/chart-js-tutorial-1-click-to-add-datapoints -->
<script>

import {afterUpdate} from 'svelte';
import {createEventDispatcher} from 'svelte';
let dispatch = createEventDispatcher();


let globalChartRef;
let avoidDublication=0;
let points = [];







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
                    dataset.data.push({
                        x: valueX,
                        y: valueY
                    });
                });
               
                     points.push({
                        x: valueX,
                        y: valueY
                    });
                
                avoidDublication+=1;
                myChart.update();

}

   const myClick = () =>{
   setTimeout(() =>{dispatch('myClick', points);});
}

function createGraph() {
    var config = {
        type: 'scatter',
        data: {
            datasets: [{
                label: "Dataset Made of Clicked Points",
                data: [],
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
                        min: -10,
                        suggestedMax: 5
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