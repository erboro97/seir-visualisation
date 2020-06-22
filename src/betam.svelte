<canvas id="myChartBeta" width="3" height="1"></canvas>

<script>
console.log("ok")

// import {afterUpdate} from 'svelte';

// export let betadata;



// let globalChartRef;
// let labels=[]
// console.log(betadata);
// let getLabels= function(){
//     for (var i=0;i<betadata.length;i++){
//         labels[i]=i;
//     }
//     return labels;
// }
// function createChart(){
//     if(globalChartRef){
//         globalChartRef.destroy();
//     }
//     globalChartRef=new Chart(document.getElementById("myChartBeta"), {
//     type: 'line',
//     data: {
//         labels: getLabels(),
//         datasets: [{ 
//             data: betadata,
//             label: "daily transmission probability",
//             borderColor: "#3e95cd",
//             fill: false
//         }
//         ]
//     },
//     options: {
//         title: {
//         display: true,
//         text: 'Transmission probability'
//         }
//     }
//     });
// }
// afterUpdate(createChart)
</script>