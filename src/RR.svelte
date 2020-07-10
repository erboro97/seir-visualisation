         
            <div class="form-check col-md-3 p-2"  style="display:inline-block;">
                <label class="form-check-label">
                    <input type="radio" bind:group={selectDiagram} value={1} class="form-check-input" name="optradio">$$R_0$$
                </label>
            </div>
            
            <div class="form-check col-md-3 p-2"  style="display:inline-block;">
                 <label class="form-check-label">
                    <input type="radio" bind:group={selectDiagram} value={2} class="form-check-input" name="optradio">$$\beta$$
                </label>
            </div>

            <div class="form-check col-md-3 p-2"  style="display:inline-block;">
                 <label class="form-check-label">
                     <input type="radio" bind:group={selectDiagram} value={3} class="form-check-input" name="optradio">$$c$$
                </label>
            </div>

            <div class="form-check col-md-3 p-2"  style="display:inline-block;">
                <label class="form-check-label">
                    <input type="radio" bind:group={selectDiagram} value={4} class="form-check-input" name="optradio">$$q$$
                </label>
        </div>
           <canvas id="myChartRadio" width="3" height="1"></canvas>
           <button id="downloadPdf" class="btn btn-primary" on:click={downloadPDF}>Download the current chart as PDF</button>

         
    
         



<script>



import {afterUpdate} from 'svelte';
import SvelteTooltip from 'svelte-tooltip';


export let data={};

let rrRadio=0;
let betaRadio=0;
let cRadio=0;
let qRadio=0;

let selectDiagram=1;

$: RRdata=data.RR;
$: betaData=data.beta;
$: cData=data.c;
$: qData=data.q;

let globalChartRef;

let labels=[]

let chartData=[];
let title;
let color;
let myLabel;



$:{

    if (selectDiagram==1){
        chartData=RRdata;
        title='Reproduction number';
        color="#3e95cd";
        myLabel="reproduction number";
    }
    if (selectDiagram==2){
        chartData=betaData;
        title='Transmission probability';
        color="#721b65";
        myLabel="Transmission probability";
    }
    if (selectDiagram==3){
        chartData=cData;
        title='Contact rate';
        color="#204051";
        myLabel="contact rate";
    }
    if (selectDiagram==4){
        chartData=qData;
        title='Quarantine rate';
        color="#2c003e";
        myLabel="quarantine rate";
    }

}
let getLabels= function(){
    
    return data.days;
}
function createChart(){
    if(globalChartRef){
        globalChartRef.destroy();
    }
    globalChartRef=new Chart(document.getElementById("myChartRadio"), {
    type: 'line',
    data: {
        labels: getLabels(),
        datasets: [{ 
            data: chartData,
            label: myLabel,
            borderColor: color,
            fill: false
        }
        ]
    },
    options: {
        title: {
        display: true,
        text: title
        }
    }
    });
}

function downloadPDF(){
   var canvas = document.querySelector('#myChartRadio');
  	var canvasImg = canvas.toDataURL("image/png", '#ffffff');

  var ctx=canvas.getContext("2d");
   ctx.fillStyle="white";
  ctx.fillRect(0,0,canvas.width,canvas.height);
	//creates image
  
	//creates PDF from img
	var doc = new jsPDF('landscape');
	doc.setFontSize(20);
	doc.addImage(canvasImg, 'PNG', 0, 0 );
    doc.save(title+'.pdf');
    createChart();
}

afterUpdate(createChart)


</script>