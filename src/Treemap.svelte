


<canvas id="treemap"></canvas>



<script>
import {afterUpdate} from 'svelte';

export let chartData;

let globalChartRef;

// console.log(chartData[1])
function getData(index){
    return chartData[1].map(function (arr){
        return arr[index];
    });
}

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
function createTreeMap(){
  var ctx=document.getElementById("treemap");
  new Chart(ctx, {
    type: 'treemap',
    data: {
        datasets: [{
            label: 'Basic treemap',
            tree: [6,6,5,4,3,2,2,1],
            fontColor: '#000',
            fontFamily: 'serif',
            fontSize: 12,
            fontStyle: 'normal',
            backgroundColor: function(ctx) {
                var value = ctx.dataset.data[ctx.dataIndex];
                var alpha = (value + 3) / 10;
                return Color('blue').alpha(alpha).rgbString();
            }
        }]
    },
});
}

afterUpdate(createTreeMap)




</script>