<script>
	import ode45 from 'ode45-cash-karp';
	import Chart from './Chart.svelte';
	import clone from 'clone';

	/Function initial values/ 
	let S0=700000;
	let E0=11800;
	let I0=1000;
	let A0=1000;
	let Sq0=0;
	let Eq0=0;
	let H0=400;
	let R0=400;

	/Parameter initial values/ 
	let c0=14.781;
	let ca=1.5;
	let q1=1.28*Math.pow(10,-5);
	let beta=1.5789*Math.pow(10,-8);
	let q0=5.631*Math.pow(10,-6);
	let sigma=1/10;
	let lambda=1/14;
	let eps=0.86834;
	let deltaI=0.13266;
	let deltaq=0.12589943;
	let gammaI=0.33029;
	let gammaA=0.13978;
	let gammaH=0.11624;
	let gammaR=3*Math.pow(10,-5);
	let theta=0.001;
	let alfab=0.4239;
	let xi=0.001;
	let b=1/2;
	let c2=35.162037286751705;

	let result=[];


	$:{


			let func = function(dydt, y, t) {
				dydt[0] = -(beta*c0+c0*q0*(1-beta))*y[0]*(y[2]+theta*y[3])+lambda*y[4]
				dydt[1] = beta*c0*(1-q0)*y[0]*(y[2]+theta*y[3])
				dydt[2] = sigma*eps*y[1]-(deltaI+gammaI)*y[2]
				dydt[3]= sigma*(1-eps)*y[1]-gammaA*y[3]+gammaR*y[7]
				dydt[4]= (1-beta)*c0*q0*y[0]*(y[2]+theta*y[3])-lambda*y[4]
				dydt[5]= beta*c0*q0*y[0]*(y[2]+theta*y[3])-lambda*y[4]
				dydt[6]= deltaI*y[2]+deltaq*y[5]-gammaH*y[6]
				dydt[7]= gammaI*y[2]+gammaA*y[3]+gammaH*y[6]-gammaR*y[7]

				// dydt[0] = y[1]
  				// dydt[1] = 4 * (1-y[0]*y[0])*y[1] - y[0]
			// dydt[2] = y[3]
			}

			// Initialize:
			let y0 = [S0, E0, I0,A0,Sq0,Eq0,H0, R0],
				t0 = 1,
				dt0 = 1,
				integrator = ode45( y0, func, t0, dt0, {tol: 5e-5, maxIncreaseFactor: 2} )
			
			// // Integrate up to tmax:
			// let tmax = 40, t = [], y = [], newElement;
			// //integrator.dtMinMag=0.5
			// while( integrator.step( tmax ) ) {
			// // Store the solution at this timestep:
			// 	integrator.dt=1
			// 	newElement=clone(integrator.y);
			// 	t.push( integrator.t )
			// 	y.push( newElement )

			// }
			let ev=1, newElement, y=[], t=[];

			while (ev<100){
				integrator.steps(10, ev);
				ev+=1;
				newElement=clone(integrator.y);
				y.push( newElement )
				t.push(integrator.t)
			}
			//console.log(t)
			//console.log(integrator.y[0]);
	

			console.log(t.length)
			result=[t,y];

				
	}
</script>

<main>
	
<div class="container">
<Chart chartData={result} />
  <h3>Initial conditions for the functions</h3>            
  <table class="table table-bordered">
    <thead>
      <tr>
        <th>Definition:</th>
        <th>Range of value:</th>
		<th>Selected value:</th>
      </tr>
    </thead>
    <tbody>
      <tr>
	  	<td>Susceptible</td>
        <td class="slidecontainer">
  		<input type="range" min="10" max="8000000000" step="800000000" bind:value={S0} class="slider" id="myRange">
		</td>
		<td>{S0}</td>
     </tr>

      <tr>
        <td>Exposed</td>
        <td class="slidecontainer">
  		<input type="range" min="10" max="1000000" step="10000" bind:value={E0} class="slider" id="myRange">
		</td>
		<td>{E0}</td>
      </tr>

	  <tr>
        <td>Infected</td>
        <td class="slidecontainer">
  		<input type="range" min="10" max="100000" step="1000" bind:value={I0} class="slider" id="myRange">
		</td>
		<td>{I0}</td>
      </tr>

	  <tr>
        <td>Asymptomatic infected</td>
        <td class="slidecontainer">
  		<input type="range" min="10" max="100000" step="1000" bind:value={A0} class="slider" id="myRange">
		</td>
		<td>{A0}</td>
      </tr>

	  <tr>
        <td>Quarantined susceptible</td>
        <td class="slidecontainer">
  		<input type="range" min="10" max="100000" step="1000" bind:value={Sq0} class="slider" id="myRange">
		</td>
		<td>{Sq0}</td>
      </tr>

	  <tr>
        <td>Quarantined exposed</td>
        <td class="slidecontainer">
  		<input type="range" min="10" max="100000" step="1000" bind:value={Eq0} class="slider" id="myRange">
		</td>
		<td>{Eq0}</td>
      </tr>

	  <tr>
        <td>Hospitalized</td>
        <td class="slidecontainer">
  		<input type="range" min="10" max="100000" step="1000" bind:value={H0} class="slider" id="myRange">
		</td>
		<td>{H0}</td>
      </tr>

	   <tr>
        <td>Recovered</td>
        <td class="slidecontainer">
  		<input type="range" min="10" max="100000" step="1000" bind:value={R0} class="slider" id="myRange">
		</td>
		<td>{R0}</td>
      </tr>
     
    </tbody>
  </table>
</div>

<div class="container">
  <h3>Initial values for the parameters</h3>            
  <table class="table table-bordered">
    <thead>
      <tr>
        <th>Definition:</th>
        <th>Range of value:</th>
		<th>Selected value:</th>
      </tr>
    </thead>
    <tbody>
      <tr>
	  	<td>Initial contact rate</td>
        <td class="slidecontainer">
  		<input type="range" min="12.48" max="45" step="0.3252" bind:value={c0} class="slider" id="myRange">
		</td>
		<td>{c0}</td>
     </tr>

      <tr>
        <td>Minimum contact rate under control strategies</td>
        <td class="slidecontainer">
  		<input type="range" min="1.0" max="6.0" step="0.05" bind:value={ca} class="slider" id="myRange">
		</td>
		<td>{ca}</td>
      </tr>

	  <tr>
        <td>Maximum contact rate under control strategies</td>
        <td class="slidecontainer">
  		<input type="range" min="0" max="1" step="0.01" bind:value={q1} class="slider" id="myRange">
		</td>
		<td>{q1}</td>
      </tr>

	  <tr>
        <td>Probability of transmission per contact</td>
        <td class="slidecontainer">
  		<input type="range" min="0" max="0.001" step="0.0001" bind:value={beta} class="slider" id="myRange">
		</td>
		<td>{beta}</td>
      </tr>

	  <tr>
        <td>Initial quarantined rate of exposed individuals</td>
        <td class="slidecontainer">
  		<input type="range" min="0" max="0.01" step="0.0001" bind:value={q0} class="slider" id="myRange">
		</td>
		<td>{q0}</td>
      </tr>

	  <tr>
        <td>Transition rate of exposed individuals to the infected class</td>
        <td class="slidecontainer">
  		<input type="range" min="0" max="1" step="0.01" bind:value={sigma} class="slider" id="myRange">
		</td>
		<td>{sigma}</td>
      </tr>

	  <tr>
        <td>Rate at which quarantined uninfects were released into the wider community</td>
        <td class="slidecontainer">
  		<input type="range" min="0" max="1" step="0.01" bind:value={lambda} class="slider" id="myRange">
		</td>
		<td>{lambda}</td>
      </tr>

	   <tr>
        <td>Probability of having symptoms among infected individuals</td>
        <td class="slidecontainer">
  		<input type="range" min="0.6" max="0.89" step="0.0029" bind:value={eps} class="slider" id="myRange">
		</td>
		<td>{eps}</td>
      </tr>
     
	 <tr>
        <td>Transition rate of symptomatic infected individuals to the quarantined infected class</td>
        <td class="slidecontainer">
  		<input type="range" min="0.01" max="0.95" step="0.0094" bind:value={deltaI} class="slider" id="myRange">
		</td>
		<td>{deltaI}</td>
      </tr>

	  <tr>
        <td>Transition rate of quarantined exposed individuals to the quarantined infected class</td>
        <td class="slidecontainer">
  		<input type="range" min="0.01" max="0.4" step="0.0039"  bind:value={deltaq} class="slider" id="myRange">
		</td>
		<td>{deltaq}</td>
      </tr>

	  <tr>
        <td>Recovery rate of symptomatic infected individuals</td>
        <td class="slidecontainer">
  		<input type="range" min="0.01" max="0.75" step="0.0074" bind:value={gammaI} class="slider" id="myRange">
		</td>
		<td>{gammaI}</td>
      </tr>

	  <tr>
        <td>Recovery rate of asymptomatic infected individuals</td>
        <td class="slidecontainer">
  		<input type="range" min="0.01" max="0.36" step="0.0035"  bind:value={gammaA} class="slider" id="myRange">
		</td>
		<td>{gammaA}</td>
      </tr>

	  <tr>
        <td>Recovery rate of quarantined infected individuals</td>
        <td class="slidecontainer">
  		<input type="range" min="0.01" max="0.1" step="0.001" bind:value={gammaH} class="slider" id="myRange">
		</td>
		<td>{gammaH}</td>
      </tr>

	  <tr>
        <td>Rate at which recovered individuals move into the pre-symptomatic class</td>
        <td class="slidecontainer">
  		<input type="range" min="0" max="0.001" step="0.00001" bind:value={gammaR} class="slider" id="myRange">
		</td>
		<td>{gammaR}</td>
      </tr>

	  <tr>
        <td>Relative transmission probability of A compared with I</td>
        <td class="slidecontainer">
  		<input type="range" min="0" max="0.1" step="0.001" bind:value={theta} class="slider" id="myRange">
		</td>
		<td>{theta}</td>
      </tr>

	  <tr>
        <td>Governmental action strength</td>
        <td class="slidecontainer">
  		<input type="range" min="0" max="1" step="0.001" bind:value={alfab} class="slider" id="myRange">
		</td>
		<td>{alfab}</td>
      </tr>

	  <tr>
        <td>The intensity of the effect of tempreture variation</td>
        <td class="slidecontainer">
  		<input type="range" min="0.001" max="0.999"  step="0.00998" bind:value={xi} class="slider" id="myRange">
		</td>
		<td>{xi}</td>
      </tr>

	  <tr>
        <td>Coefficient for contact rate function</td>
        <td class="slidecontainer">
  		<input type="range" min="0.5" max="1" step="0.005" bind:value={b} class="slider" id="myRange">
		</td>
		<td>{b}</td>
      </tr>

	  
    </tbody>
  </table>
</div>

</main>

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
  height: 25px; /* Specified height */
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
  width: 25px; /* Set a specific slider handle width */
  height: 25px; /* Slider handle height */
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