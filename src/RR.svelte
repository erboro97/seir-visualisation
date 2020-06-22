<canvas id="myChartRR" width="3" height="1"></canvas>

<script>
import {afterUpdate} from 'svelte';

export let RRdata;

let globalChartRef;
let labels=[]

console.log(RRdata.length);
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
    globalChartRef=new Chart(document.getElementById("myChartRR"), {
    type: 'line',
    data: {
        labels: getLabels(),
        datasets: [{ 
            data: RRdata,
            label: "reproduction number",
            borderColor: "#3e95cd",
            fill: false
        }
        ]
    },
    options: {
        title: {
        display: true,
        text: 'Reproduction number'
        }
    }
    });
}
afterUpdate(createChart)
</script>