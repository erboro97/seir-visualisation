

<canvas id="myChart_SIR" width="3" height="2"></canvas>
Select the number of day for more details
<input type="range" min="0" max="98" step="1" bind:value={day} class="slider" id="myRange">
{day}. day
<canvas id="treemap_SIR" width="3" height="1"></canvas>
<button id="downloadPdf_SIR" class="btn btn-primary" on:click={downloadPDF}>Download Bar Chart as PDF</button>

<button id="downloadPdf_SIR"  class="btn btn-primary" on:click={downloadPDFTreeMap}>Download Treemap as PDF</button>




<script>


import {afterUpdate} from 'svelte';
import toDataURL from 'canvas-background';

export let chartData_SIR={};

let globalChartRef;
let globalTreemapRef;
let day=0;
$:  treemapData=chartData_SIR[1][0];
let treemapChartData=[];


$:{


  treemapData=chartData_SIR[1][day];
  treemapChartData=[{num:treemapData[0],tag:"Suscpetible"},{num:treemapData[1],tag:"Infected"},{num:treemapData[2],tag:"Recovered" }];

}

// console.log(chartData[1])
function getData(index){
    return chartData_SIR[1].map(function (arr){
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
  var ctx=document.getElementById("treemap_SIR");
  if (globalTreemapRef!=undefined){
    globalTreemapRef.destroy();
  }
   globalTreemapRef = new Chart(ctx, {
    type: 'treemap',
    data: {
        datasets: [{
      
            tree: treemapChartData,
            key:['num'],
            groups:['tag'],
            fontColor: '#000',
            fontFamily: 'serif',
            fontSize: 12,
            fontStyle: 'normal',
            backgroundColor: ['rgb(255, 159, 64)','rgb(255, 205, 86)','rgb(75, 192, 192)']
        }]
    },
     options: {
    legend: { display: false },
    tooltips: { enabled: false }
  }
});
}

afterUpdate(createTreeMap)


function createTestChart(){
  if(globalChartRef){
      globalChartRef.destroy();
    } 
  
  var barChartData = {
      labels: chartData_SIR[2],
			datasets: [   
         {
        label: 'Infected',
        backgroundColor: window.chartColors.green,
				data: 
					getData(1)
				
      },
      
      {
        label: 'Recovered',
        backgroundColor: window.chartColors.aqua,
				data: 
					getData(2)
				
      },
      {
        label: 'Susceptible',
        backgroundColor: window.chartColors.grey,
				data: 
          getData(0)
				
      }
      ]

	};
    
    
   globalChartRef = new Chart(document.getElementById("myChart_SIR"), {
 	      type: 'bar',
        data: barChartData,
				options: {
				
			
					scales: {

						xAxes: [{
							stacked: true,
						}],
						yAxes: [{
              stacked: true,
              ticks: {
                    suggestedMin: 0
                }
						}]
          },
       
        },

});


}

function downloadPDF(){
   var canvas = document.querySelector('#myChart_SIR');
  	var canvasImg = canvas.toDataURL("image/png", '#ffffff');

  var ctx=canvas.getContext("2d");
   ctx.fillStyle="white";
  ctx.fillRect(0,0,canvas.width,canvas.height);
	//creates image
  
	//creates PDF from img
	var doc = new jsPDF('landscape');
	doc.setFontSize(20);
	doc.addImage(canvasImg, 'PNG', 0, 0 );
  doc.save('barchart.pdf');
  
  createTestChart()
}
function downloadPDFTreeMap(){
   var canvas = document.querySelector('#treemap_SIR');
  	var canvasImg = canvas.toDataURL("image/png", '#ffffff');

  var ctx=canvas.getContext("2d");
   ctx.fillStyle="white";
  ctx.fillRect(0,0,canvas.width,canvas.height);
	//creates image
  
	//creates PDF from img
	var doc = new jsPDF('landscape');
	doc.setFontSize(20);
	doc.addImage(canvasImg, 'PNG', 0, 0 );
  doc.save('treemap_SIR.pdf');
  
  createTreeMap();
}

afterUpdate(createTestChart)
</script>

<style>
  
   
        

	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}


	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}

	.slidecontainer {
  width: 50%; /* Width of the outside container */
}

/* The slider itself */
.slider {
  -webkit-appearance: none;  /* Override default CSS styles */
  appearance: none;
  width: 100%; /* Full-width */
  height: 13px; /* Specified height */
  background: #d3d3d3; /* Grey background */
  outline: none; /* Remove outline */
  opacity: 0.7; /* Set transparency (for mouse-over effects on hover) */
  -webkit-transition: .2s; /* 0.2 seconds transition on hover */
  transition: opacity .2s;
}

/* Mouse-over effects */
.slider:hover {
  opacity: 1; /* Fully shown on mouse-over */
}

/* The slider handle (use -webkit- (Chrome, Opera, Safari, Edge) and -moz- (Firefox) to override default look) */
.slider::-webkit-slider-thumb {
  -webkit-appearance: none; /* Override default look */
  appearance: none;
  width: 13px; /* Set a specific slider handle width */
  height: 13px; /* Slider handle height */
  background: #4CAF50; /* Green background */
  cursor: pointer; /* Cursor on hover */
}

.slider::-moz-range-thumb {
  width: 25px; /* Set a specific slider handle width */
  height: 25px; /* Slider handle height */
  background: #4CAF50; /* Green background */
  cursor: pointer; /* Cursor on hover */
}
</style>
