<canvas id="myChart" width="3" height="1"></canvas>


<script>
import {afterUpdate} from 'svelte';

export let chartData;
function getData(index){
    return chartData[1].map(function (arr){
        return arr[index];
    });
}
// console.log("ok");
// console.log(getData(0));
function createTestChart(){

    new Chart(document.getElementById("myChart"), {
  type: 'line',
  data: {
    labels: chartData[0],
    datasets: [{ 
        data: getData(0),
        label: "Susceptible",
        borderColor: "#3e95cd",
        fill: false
      }, { 
        data: getData(1),
        label: "Expsed",
        borderColor: "#8e5ea2",
        fill: false
      }, { 
        data: getData(1),
        label: "Europe",
        borderColor: "#3cba9f",
        fill: false
      }, { 
        data: getData(2),
        label: "Latin America",
        borderColor: "#e8c3b9",
        fill: false
      }, { 
        data: getData(3),
        label: "North America",
        borderColor: "#c45850",
        fill: false
      }
    ]
  },
  options: {
    title: {
      display: true,
      text: 'World population per region (in millions)'
    }
  }
});
}

afterUpdate(createTestChart)
</script>