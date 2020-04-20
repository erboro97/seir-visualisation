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
// console.log("ok");
function createTestChart(){

    new Chart(document.getElementById("myChart"), {
  type: 'line',
  data: {
    labels: chartData[2],
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
        data: getData(2),
        label: "Symptomatic infected",
        borderColor: "#3cba9f",
        fill: false
      }, { 
        data: getData(3),
        label: "Asymptomatic infected",
        borderColor: "#e8c3b9",
        fill: false
      }, { 
        data: getData(4),
        label: "Quarantined susceptible",
        borderColor: "#c45850",
        fill: false
      },{ 
        data: getData(5),
        label: "Quarantined exposed",
        borderColor: "#ff7f50",
        fill: false
      },{ 
        data: getData(6),
        label: "Quarantined infected",
        borderColor: "#b8860b",
        fill: false
      },{ 
        data: getData(7),
        label: "Recovered",
        borderColor: "#006400",
        fill: false
      }

    ]
  },
  options: {
    title: {
      display: true,
      text: 'Epidemiological model'
    }
  }
});
}

afterUpdate(createTestChart)
</script>