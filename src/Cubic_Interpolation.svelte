<canvas id="ChartCnvs"  width="3" height="2" on:click|preventDefault={myClick}></canvas>


<!-- https://steemit.com/utopian-io/@loshcat/chart-js-tutorial-1-click-to-add-datapoints -->
<script>

import {afterUpdate} from 'svelte';
import {createEventDispatcher} from 'svelte';
let dispatch = createEventDispatcher();
export let temp;

let globalChartRef;
let avoidDublication=0;
let points = [];
let mySet=[];
let step=10;


window.chartColors = {
      red: 'rgb(255, 99, 132)',
      orange: 'rgb(255, 159, 64)',
      yellow: 'rgb(255, 205, 86)',
      green: 'rgb(75, 192, 192)',
      blue: 'rgb(54, 162, 235)',
      purple: 'rgb(153, 102, 255)',
      grey: 'rgb(201, 203, 207)',
      aqua: '#7FFFD4'
};



function click(element, dataAtClick, myChart){
    let scaleRef, dataset, 
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
    myChart.data.datasets.forEach((dat) => {
                  dataset=dat;
                });

                dataset.data.forEach((element) => {
                            if(Math.ceil(valueX / step) * step===element.x){
                                element.y=valueY;
                            }

                           
                     })
                      points=dataset.data;
               
                myChart.update()

                //clearTimeout(myVar);
               

}



const changeStep = (e) =>{
    step=parseInt(e.target.value)

}

const linearPoints = () =>{
    mySet=[];
    for (var i=0;i<=100;i+=step){
        mySet.push({x:i,y:temp[i]});
    }
    return mySet
}

const myClick = () =>{
   setTimeout(() =>{dispatch('myClick', points);});
}
function createGraph() {
    var config = {
        type: 'scatter',
        labels: ['10','20','30','40','50','60','70'],
        data: {
            datasets: [{
                label: "Adjustable Interpolation with Control Points",
                data:  linearPoints(),               
                fill: false,
                showLine: true,
                cubicInterpolationMode: 'monotone'
            }]
        },
        options: {
            devicePixelRatio: 4,
            onClick: function (element, dataAtClick) {
                click(element,dataAtClick, this);
            },
            title: {
                display: false,
                text: "Temperature"
            },
            scales: {
                 xAxes: [{
                    type: "linear",
                    display: true,
                    scaleLabel: {
                        display: true,
                        labelString: 'Days'
                    },
                    ticks: {
                        min: 0,
                        suggestedMax: 100
                    }
                }, ],
                yAxes: [{
                    display: true,
                    scaleLabel: {
                        display: true,
                        labelString: 'Temperature [C]'
                    },
                    ticks: {
                        beginAtZero: true,
                        suggestedMax: 40,
                        suggestedMin:-40
                    }
                }]
            },
            responsive: true,
            maintainAspectRatio: true
        }
    };
    
    config.data.datasets.forEach(function (dataset) {
        dataset.borderColor = window.chartColors.red;
        dataset.backgroundColor = window.chartColors.red;
        dataset.pointBorderColor = window.chartColors.red;
        dataset.pointBackgroundColor = window.chartColors.red;
        dataset.pointRadius = 7;
        dataset.pointBorderWidth = 2;
        dataset.pointHoverRadius = 8;
    });
    
    var ctx = document.getElementById('ChartCnvs').getContext('2d');
     if (globalChartRef) {
        globalChartRef.destroy();
      }
    globalChartRef = new Chart(ctx, config);    
 
}
afterUpdate(createGraph)
</script>

<main>
    <div class="form-group">
         <strong style="display:inline-block;"> Stepsize:</strong><input type="number" name="c" class="form-control" on:change|stopPropagation ={changeStep} value={step}>
    </div>
</main>