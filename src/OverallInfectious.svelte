<script>
import {afterUpdate} from 'svelte';
import toDataURL from 'canvas-background';


export let resultsOverall={};

let globalChartRef;



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
  if(globalChartRef){
      globalChartRef.destroy();
    } 
  
  var lineChartData = {
      labels: resultsOverall[4],
			datasets: [   
         {
        label: 'SEIR plus',
        backgroundColor: window.chartColors.yellow,
				data: 
					resultsOverall[2][0]
				
      },
      {
        label: 'SEIR',
        backgroundColor: window.chartColors.green,
				data: 
					resultsOverall[2][1]
				
      },
      
      {
        label: 'SIR',
        backgroundColor: window.chartColors.aqua,
				data: 
					resultsOverall[2][2]
				
      }
      ]

    };
    globalChartRef = new Chart(document.getElementById("InfectiousChart"), {
 	      type: 'line',
            data: lineChartData,
				options: {
				
			
					scales: {

						xAxes: [{
							stacked: false,
						}],
						yAxes: [{
              stacked: false,
              ticks: {
                    suggestedMin: 0
                }
						}]
          },
       
        },

});


}

    
afterUpdate(createTestChart)


</script>

<canvas id="InfectiousChart" width="3" height="2"></canvas>
