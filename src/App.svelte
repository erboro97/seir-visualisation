<script>
	import ode45 from 'ode45-cash-karp';
	import Chart from './Chart.svelte';
	import Cubic from './Cubic_interpolation.svelte';
	import clone from 'clone';
	import Spline from 'cubic-spline';

	/Function initial values/ 
	let N=7000000;
	let S0=N*0.9;
	let E0=11800;
	let I0=1000;
	let A0=1000;
	let F0=0;
	let Rv0=0;
	let H0=400;
	let R0=400;

	/Parameter initial values/ 
	let c1=14.781;  //!
	let c2=35.162037286751705; 
	let c3=14.781; //!
	let beta1=1.5789*Math.pow(10,-8); //!
	let beta2=1.5789*Math.pow(10,-8); //!
	let beta3=1.5789*Math.pow(10,-8); //!
	let gammaI=0.33029;
	let gammaA=0.13978;
	let gammaH=0.11624;
	let sigma=1/10;
	let rho =0.5; //!
	let tau = 0.5 //!
	let alfa=0.4239; //!
	let deltaI=0.5 //!

	

	let result=[];
	let points=[];

	


	$:{

		// Cubic interpolation ---- https://www.npmjs.com/package/cubic-spline
		let xCoord = [];
		let yCoord = [];
		points.forEach(point => {
			xCoord.push(point.x);
			yCoord.push(point.y);
		});

		if (Array.isArray(xCoord) && xCoord.length) {
			const spline = new Spline(xCoord, yCoord);
			console.log("Cubic");
			for (let i = 0; i < 6; i++) {
 				console.log(spline.at(i * 0.1));
			}
		}
		
		// Differential equation solver
			S0=N*0.9;
			let func = function(dydt, y, t) {
				// dydt[0] = -(beta*c0+c0*q0*(1-beta))*y[0]*(y[2]+theta*y[3])+lambda*y[4]
				// dydt[1] = beta*c0*(1-q0)*y[0]*(y[2]+theta*y[3])
				// dydt[2] = sigma*eps*y[1]-(deltaI+gammaI)*y[2]
				// dydt[3]= sigma*(1-eps)*y[1]-gammaA*y[3]+gammaR*y[7]
				// dydt[4]= (1-beta)*c0*q0*y[0]*(y[2]+theta*y[3])-lambda*y[4]
				// dydt[5]= beta*c0*q0*y[0]*(y[2]+theta*y[3])-lambda*y[4]
				// dydt[6]= deltaI*y[2]+deltaq*y[5]-gammaH*y[6]
				// dydt[7]= gammaI*y[2]+gammaA*y[3]+gammaH*y[6]-gammaR*y[7]

				dydt[0] = -beta1*c1*y[0]*y[3]/N-beta2*c2*y[0]*y[2]/N-beta3*c3*y[0]*y[4]/N
				dydt[1] = beta1*c1*y[0]*y[3]/N+beta2*c2*y[0]*y[2]/N+beta3*c3*y[0]*y[4]/N-sigma*y[1]
				dydt[2] = sigma*(1-rho)*y[1]-(gammaA+tau)*y[2]
				dydt[3]= sigma*rho*y[1]+tau*y[2]-(gammaI+deltaI)*y[3]
				dydt[4]= gammaI*y[3]-(alfa+gammaH)*y[4]
				dydt[5]= gammaH*y[4]
				dydt[6]= alfa*y[4]
				dydt[7]= gammaA*y[2]

				// dydt[0] = y[1]
  				// dydt[1] = 4 * (1-y[0]*y[0])*y[1] - y[0]
			// dydt[2] = y[3]
			}

			// Initialize:
			let y0 = [S0, E0,A0,I0,H0,R0,F0,Rv0],
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
			let ev=1, newElement, y=[], t=[],days=[];

			while (ev<100){
				integrator.steps(10, ev);
				days.push(ev);
				ev+=1;
				newElement=clone(integrator.y);
				y.push( newElement )
				t.push(integrator.t)
			}
			//console.log(t)
			//console.log(integrator.y[0]);
	

			result=[t,y,days];


				
	}

const myClick = (e) =>{
	points=e.detail;
}
</script>

<main>
	
<div class="container">
<Chart chartData={result} />
<Cubic on:myClick={myClick}/>
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
	  	<td>Population</td>
        <td class="slidecontainer">
  		<input type="range" min="10" max="8000000000" step="8000000" bind:value={N} class="slider" id="myRange">
		</td>
		<td>{N}</td>
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
        <td>F</td>
        <td class="slidecontainer">
  		<input type="range" min="10" max="100000" step="1000" bind:value={F0} class="slider" id="myRange">
		</td>
		<td>{F0}</td>
      </tr>

	  <tr>
        <td>Rv</td>
        <td class="slidecontainer">
  		<input type="range" min="10" max="100000" step="1000" bind:value={Rv0} class="slider" id="myRange">
		</td>
		<td>{Rv0}</td>
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
	  	<td>c1</td>
        <td class="slidecontainer">
  		<input type="range" min="12.48" max="45" step="0.3252" bind:value={c1} class="slider" id="myRange">
		</td>
		<td>{c1}</td>
     </tr>

      <tr>
        <td>c2</td>
        <td class="slidecontainer">
  		<input type="range" min="1.0" max="6.0" step="0.05" bind:value={c2} class="slider" id="myRange">
		</td>
		<td>{c2}</td>
      </tr>

      <tr>
        <td>c3</td>
        <td class="slidecontainer">
  		<input type="range" min="1.0" max="6.0" step="0.05" bind:value={c3} class="slider" id="myRange">
		</td>
		<td>{c3}</td>
      </tr>

	  <tr>
        <td>beta1</td>
        <td class="slidecontainer">
  		<input type="range" min="0" max="0.001" step="0.0001" bind:value={beta1} class="slider" id="myRange">
		</td>
		<td>{beta1}</td>
      </tr>

	  <tr>
        <td>beta2</td>
        <td class="slidecontainer">
  		<input type="range" min="0" max="0.001" step="0.0001" bind:value={beta2} class="slider" id="myRange">
		</td>
		<td>{beta2}</td>
      </tr>

	  <tr>
        <td>beta3</td>
        <td class="slidecontainer">
  		<input type="range" min="0" max="0.001" step="0.0001" bind:value={beta3} class="slider" id="myRange">
		</td>
		<td>{beta3}</td>
      </tr>


	  <tr>
        <td>Transition rate of exposed individuals to the infected class</td>
        <td class="slidecontainer">
  		<input type="range" min="0" max="1" step="0.01" bind:value={sigma} class="slider" id="myRange">
		</td>
		<td>{sigma}</td>
      </tr>

     
	 <tr>
        <td>Transition rate of symptomatic infected individuals to the quarantined infected class</td>
        <td class="slidecontainer">
  		<input type="range" min="0.01" max="0.95" step="0.0094" bind:value={deltaI} class="slider" id="myRange">
		</td>
		<td>{deltaI}</td>
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
        <td>Alfa</td>
        <td class="slidecontainer">
  		<input type="range" min="0" max="1" step="0.001" bind:value={alfa} class="slider" id="myRange">
		</td>
		<td>{alfa}</td>
      </tr>
	  <tr>
        <td>Rho</td>
        <td class="slidecontainer">
  		<input type="range" min="0" max="1" step="0.001" bind:value={rho} class="slider" id="myRange">
		</td>
		<td>{rho}</td>
      </tr>
	  <tr>
        <td>tau</td>
        <td class="slidecontainer">
  		<input type="range" min="0" max="1" step="0.001" bind:value={tau} class="slider" id="myRange">
		</td>
		<td>{tau}</td>
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