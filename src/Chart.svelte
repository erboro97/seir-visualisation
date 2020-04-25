<canvas id="myChart" width="3" height="1"></canvas>


<script>
import {afterUpdate} from 'svelte';

export let chartData;

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


function createTestChart(){
  
  var barChartData = {
      labels: chartData[2],
			datasets: [{
        label: 'Dataset 1',
        backgroundColor: window.chartColors.red,
				data: 
          getData(0)
				
			}, {
        label: 'Dataset 2',
         backgroundColor: window.chartColors.yellow,
				data: 
					getData(1)
				
			}, {
        label: 'Dataset 3',
        backgroundColor: window.chartColors.green,
				data: 
					getData(2)
				
      },
      {
        label: 'Dataset 3',
        backgroundColor: window.chartColors.orange,
				data: 
					getData(3)
				
      },
      {
        label: 'Dataset 3',
        backgroundColor: window.chartColors.blue,
				data: 
					getData(4)
				
      },
      {
        label: 'Dataset 3',
        backgroundColor: window.chartColors.purple,
				data: 
					getData(5)
				
      },

      {
        label: 'Dataset 3',
        backgroundColor: window.chartColors.grey,
				data: 
					getData(6)
				
      },
      {
        label: 'Dataset 3',
        backgroundColor: window.chartColors.aqua,
				data: 
					getData(7)
				
      }]

		};

    new Chart(document.getElementById("myChart"), {
 	      type: 'bar',
				data: barChartData,
				options: {
					title: {
						display: true,
						text: 'Chart.js Bar Chart - Stacked'
					},
					tooltips: {
						mode: 'index',
						intersect: false
					},
					scales: {
						xAxes: [{
							stacked: true,
						}],
						yAxes: [{
							stacked: true
						}]
					}
				}
});
}

afterUpdate(createTestChart)
</script>