<div class="container">
    <div class="col">
        <div class="row-sm">
            <div class="form-check">
                <label class="form-check-label">
                    <input type="radio" bind:group={selectDiagram} value={1} class="form-check-input" name="optradio">$$R_0$$
                </label>
            </div>
        </div>
        <div class="row-sm">
            <div class="form-check">
                 <label class="form-check-label">
                    <input type="radio" bind:group={selectDiagram} value={2} class="form-check-input" name="optradio">$$\beta$$
                </label>
            </div>
        </div>
        <div class="row-sm">
            <div class="form-check disabled">
                 <label class="form-check-label">
                     <input type="radio" bind:group={selectDiagram} value={3} class="form-check-input" name="optradio">$$q$$
                </label>
            </div>
        </div>
        <div class="row-sm">
            <div class="form-check disabled">
                <label class="form-check-label">
                    <input type="radio" bind:group={selectDiagram} value={4} class="form-check-input" name="optradio">$$c$$
                </label>
            </div>
        </div>
    </div>
    <div class="col">
        <div class="row-sm">
            <canvas id="myChartRadio" width="3" height="1"></canvas>
    
         
         </div>
    </div>
</div>


<script>
let rrRadio=0;
let betaRadio=0;
let cRadio=0;
let qRadio=0;

let selectDiagram=1;


import {afterUpdate} from 'svelte';
import SvelteTooltip from 'svelte-tooltip';


export let data;

let RRdata=data.RR;
let betaData=data.beta;
let cData=data.c;
let qData=data.q;

let globalChartRef;

let labels=[]

let chartData=[];
let title;
let color;
let myLabel;

$:{
    console.log(selectDiagram);
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
    for (var i=0;i<RRdata.length;i++){
        labels[i]=i;
    }
    return labels;
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
afterUpdate(createChart)



</script>