

<script>
	import ode45 from 'ode45-cash-karp';
	import Chart from './Chart.svelte';
	import betaCh from './betaChart.svelte';
	import RRChart from './RR.svelte';
	import Cubic from './Cubic_Interpolation.svelte';
	import CubicTau from './Cubic_Interpolation_tau.svelte';
	import clone from 'clone';
	import Spline from 'cubic-spline';
	import SvelteTooltip from 'svelte-tooltip';

	let days=99;
	let ev=1;

	/Function initial values/ 
	let N=52740000;
	let S0=52740000;
	let E0=11800;
	let I0=2220;
	let A0=2220;
	let Sq0=0;
	let Eq0=0;
	let H0=444;
	let R0=28;

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
	let rho =0.86834; //!
	let tau = 0.5 //!
	let alfa=0.4239; //!
	let deltaI=0.13266 //!

	let w=1.2;
	let lambda=1/14;
	let f=1.2;
	let gammaR=3*Math.pow(10,-5);;
	let g=1.2;
	let deltaq=0.12589;
	let h=1.2;
	let q1=1.28*Math.pow(10,-5); // nem fog kelleni
	let theta=0.001;
	let ca=1.5;
	let c0=14.781;
	let b=0.25;
	let q0=5.631*Math.pow(10,-6);;
	let alfab=0.4239;
	let beta0=1.57*Math.pow(10,-8);;
	let eps=0.82;
	let xi=0.0000000000001


	let result=[];
	let ct=Array.apply(null, Array(days)).map(Number.prototype.valueOf,0);; // tempreture
	let qt=Array.apply(null, Array(days)).map(Number.prototype.valueOf,0);; // humidity
	let pointsC=[];
	let pointsq=[];

	let cStep=10;

	let RR=[];
	let betam=[];
	let q=[];
	let c=[];

	let data={};
	let countries = [
		{ id: 1, text: 'North-Italy' },
		{ id: 2, text: 'Hubei' }
	];
	let selectedCountry={};
	let tempreture=[6, 7, 7, 10, 10, 9, 8, 5, 4, 3, 7, 10, 8, 8, 3, 4, 6, 8, 10, 11, 9, 7, 7, 7, 6, 5, 4, 8, 9, 13, 11, 12, 13, 12, 14, 14, 7, 8, 10, 14, 11, 11, 16, 17, 17, 2, 7, 11, 12, 14, 15, 15, 17, 17, 22, 20, 17, 11, 9, 14, 15, 10, 9, 13, 14, 13, 17, 18, 13, 16, 17, 17, 16, 18, 20, 18, 20, 21, 22, 24, 22, 21, 22, 23, 23, 24, 10, 10, 9, 13, 13, 16, 17, 18, 19, 20, 20, 21, 22, 26, 17]
	let relativeHumidity=[68, 78, 82, 75, 88, 92, 78, 77, 89, 93, 77, 65, 68, 63, 84, 79, 69, 64, 54, 50, 77, 84, 87, 87, 83, 85, 74, 63, 63, 53, 51, 59, 62, 57, 59, 57, 81, 76, 68, 62, 69, 83, 76, 83, 82, 84, 50, 41, 44, 54, 55, 58, 59, 56, 64, 68, 78, 83, 86, 69, 65, 78, 77, 62, 60, 76, 73, 81, 82, 58, 52, 65, 67, 54, 43, 50, 57, 56, 51, 43, 71, 71, 65, 65, 77, 74, 78, 74, 83, 69, 71, 67, 66, 55, 53, 48, 48, 48, 49, 45, 70]
	$:{
		ev=1
		// Cubic interpolation ---- https://www.npmjs.com/package/cubic-spline
		let xCoordC = [];
		let yCoordC = [];


		let xCoordq = [];
		let yCoordq = [];

		pointsC.forEach(point => {
			xCoordC.push(point.x);
			yCoordC.push(point.y);
		});

		pointsq.forEach(point => {
			xCoordq.push(point.x);
			yCoordq.push(point.y);
		});
		if (Array.isArray(xCoordC) && xCoordC.length) {
			const spline = new Spline(xCoordC, yCoordC);
			for (let i = 0; i < 90; i++) {
				 ct[i]=spline.at(i);
				 if (ct[i]==NaN && ct[i]==undefined){
					 ct[i]=0;
				 }
			}
		}
		if (Array.isArray(xCoordq) && xCoordq.length) {
			const spline = new Spline(xCoordq, yCoordq);
			for (let i = 0; i < 90; i++) {
 				qt[i]=spline.at(i);
				 if (qt[i]==NaN && qt[i]==undefined){
					 qt[i]=0;
				 }
			}
		}
			let AH=function(){
				if (ct[ev]!=undefined || qt[ev]!=undefined){
					return 6.112*Math.exp(17.67*ct[ev]/(ct[ev]+243.5))*qt[ev]*2.1674/(273.15+ct[ev]);
				}
				return 6.112*Math.exp(17.67*c1/(c1+243.5))*q1*2.1674/(273.15+c1);
			}

			let betaFunc = function(y){
				return (1-alfab)*beta0*(1+xi*AH())*Math.pow((1-(y[2]+y[3])/(y[0]+y[7])),2);
			}
			let cFunc = function(){
				return ca+3*(c0-ca)/(1+Math.pow(b,-ev));
			}
			let qFunc = function(){
				return (q1*ev+q0)/(ev+1);
			}
			let RRFunc = function(y){
				return (betaFunc(y)*eps*cFunc()*(1-qFunc())/(deltaI+alfa+gammaI)+betaFunc(y)*cFunc()*theta*(1-eps)*(1-qFunc())/gammaA)*y[0];
			}
		// Differential equation solver
			let wFunct = function(y){
				// if (ct[ev]!=undefined || qt[ev]!=undefined){
				// 	return [beta1*ct[ev]+ct[ev]*qt[ev]*(1-beta1)]*y[0]*(y[2]+theta*y[3]);
				// }
				// return [beta1*c1+c1*q1*(1-beta1)]*y[0]*(y[2]+theta*y[3]);
				return (betaFunc(y)*cFunc()+cFunc()*qFunc()*(1-betaFunc(y)))*y[0]*(y[2]+theta*y[3]);
				//return (betaFunc(y)*c0+c0*qFunc()*(1-betaFunc(y)))*y[0]*(y[2]+theta*y[3]);
			}

			let fFunct= function(y){
				
				return betaFunc(y)*cFunc()*(1-qFunc())*y[0]*(y[2]+theta*y[3]);
				//return betaFunc(y)*c0*(1-qFunc())*y[0]*(y[2]+theta*y[3]);
			}

			let gFunct=function(y){

				return (1-betaFunc(y))*cFunc()*qFunc()*y[0]*(y[2]+theta*y[3]);
				//return (1-betaFunc(y))*c0*qFunc()*y[0]*(y[2]+theta*y[3]);
			}
			let hFunct=function(y){
				
				return betaFunc(y)*cFunc()*qFunc()*y[0]*(y[2]+theta*y[3]);
				//return betaFunc(y)*c0*qFunc()*y[0]*(y[2]+theta*y[3]);
			}

			
			let func = function(dydt, y, t) {
				RR[ev]=RRFunc(y);
				betam[ev]=betaFunc(y);
				c[ev]=cFunc();
				q[ev]=qFunc();
				dydt[0] =-wFunct(y)+lambda*y[4]; //S
				dydt[1] = fFunct(y)+sigma*y[1];  //E
				dydt[2] = sigma*rho*y[1]-(deltaI+alfa+gammaI)*y[2]; //I
				dydt[3]= sigma*(1-rho)*y[1]-gammaA*y[3]+gammaR*y[7] //A
				dydt[4]= gFunct(y)-lambda*y[4] //Sq
				dydt[5]= hFunct(y)-y[4]*y[5] //Eq
				dydt[6]= deltaI*y[2]+deltaq*y[5]-(alfa+gammaH)*y[6]; //H
				dydt[7]= gammaI*y[2]+gammaA*y[3]+gammaH*y[6]-gammaR*y[7]; //R
			}

			// Initialize:
			let y0 = [S0, E0,I0,A0,Sq0,Eq0,H0,R0],
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
			let newElement, y=[], t=[],days=[];

			while (ev<100){

				integrator.steps(1, ev);
				days.push(ev);
				ev+=1;
				newElement=clone(integrator.y);
				y.push( newElement )
				t.push(integrator.t)
			}
	

			result=[t,y,days];

			data.RR=RR;
			data.beta=betam;
			data.c=c;
			data.q=q;
				
	}

const myClickC = (e) =>{
		pointsC=e.detail;
}

const myClickTau = (e) =>{
	pointsq=e.detail;
}

const changeInitialValue=()=>{
	if (selectedCountry.id==1){

	}
	else{
			S0=52740000.0;
			E0=19.220820541214668;
			I0=16.925617788469424;
			A0=17.99157876590649;
			Sq0=15.538827852131071;
			Eq0=1.7173986822830976;
			H0=46.0107243219873;
			R0=3.0748316104908433;
			c0=41.852549488391446;
			beta0=2.1031894440837713*Math.pow(10,-12);
			q0=1.484170130317585*Math.pow(10,-7);
			sigma=0.3333;
			lambda=0.071428;
			eps=0.88;
			deltaI=0.121;
			deltaq=0.03363;
			gammaI=0.1351146;
			gammaA=0.0117950;
			gammaH=0.028635;
			alfa=0.0021171;
			theta=0.00016357;
			c2=1.712150*Math.pow(10,-7);
			b=0.908;
			alfab=0.0012209;
			xi=0.1127126;
			gammaR=1.714578192*Math.pow(10,-6);
			ca=1.287039726
			tempreture=[6, 7, 7, 10, 10, 9, 8, 5, 4, 3, 7, 10, 8, 8, 3, 4, 6, 8, 10, 11, 9, 7, 7, 7, 6, 5, 4, 8, 9, 13, 11, 12, 13, 12, 14, 14, 7, 8, 10, 14, 11, 11, 16, 17, 17, 2, 7, 11, 12, 14, 15, 15, 17, 17, 22, 20, 17, 11, 9, 14, 15, 10, 9, 13, 14, 13, 17, 18, 13, 16, 17, 17, 16, 18, 20, 18, 20, 21, 22, 24, 22, 21, 22, 23, 23, 24, 10, 10, 9, 13, 13, 16, 17, 18, 19, 20, 20, 21, 22, 26,17]
			relativeHumidity=[68, 78, 82, 75, 88, 92, 78, 77, 89, 93, 77, 65, 68, 63, 84, 79, 69, 64, 54, 50, 77, 84, 87, 87, 83, 85, 74, 63, 63, 53, 51, 59, 62, 57, 59, 57, 81, 76, 68, 62, 69, 83, 76, 83, 82, 84, 50, 41, 44, 54, 55, 58, 59, 56, 64, 68, 78, 83, 86, 69, 65, 78, 77, 62, 60, 76, 73, 81, 82, 58, 52, 65, 67, 54, 43, 50, 57, 56, 51, 43, 71, 71, 65, 65, 77, 74, 78, 74, 83, 69, 71, 67, 66, 55, 53, 48, 48, 48, 49, 45, 70]

	}
}




</script>



<main>
	<div class="container">
		<div class="panel-group ">
			<div class="panel panel-danger" style="display:inline-block;">
				 <div class="panel-heading">Select a template:</div>
				 <div class="panel-body">
					<select bind:value={selectedCountry} on:click={changeInitialValue}>
						{#each countries as country}
							<option value={country}>
								{country.text}
							</option>
						{/each}
					</select>
				</div>
			</div>
		</div>

	<div class="panel-group "  >
	<div class="panel panel-success  col-md-6 p-2" style="display:inline-block;">
	 <div class="panel-heading">Data visualization</div>
	 <div class="panel-body">
				<Chart chartData={result} />

	</div>
	</div>
	</div>

	<div class="panel-group col-md-6 p-2 "  style="display:inline-block;">
	<div class="panel panel-success">
	 <div class="panel-heading">Data visualization</div>
	 <div class="panel-body">
		<RRChart data={data}/>

	</div>
	</div>
	</div>


		<div class="panel-group col-md-6 p-2 "  style="display:inline-block;">
	<div class="panel panel-warning">
	 <div class="panel-heading">Select values of tempreture!</div>
	 <div class="panel-body">
<Cubic temp={tempreture} on:myClick={myClickC}/>
	</div>
	</div>
	</div>

		<div class="panel-group col-md-6 p-2 "  style="display:inline-block;">
	<div class="panel panel-warning">
	 <div class="panel-heading">Select values of absolute humid!</div>
	 <div class="panel-body">
				<CubicTau humidity={relativeHumidity} on:myClick={myClickTau}/>

	</div>
	</div>
	</div>

				

		<div class="panel-group col-md-6 p-2 "  style="display:inline-block;">
	<div class="panel panel-info">
	 <div class="panel-heading">Change initial conditions!</div>
	 <div class="panel-body">
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
	
							<SvelteTooltip tip="Susceptible" left ><td>$$S$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="10" max="800000000" step="800000" bind:value={S0} class="slider" id="myRange">
							</td>
							<td>{S0}</td>
						</tr>
						<tr>
							<SvelteTooltip tip="Exposed" left ><td>$$E$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="10" max="1000000" step="10000" bind:value={E0} class="slider" id="myRange">
							</td>
							<td>{E0}</td>
						</tr>
						<tr>
							<SvelteTooltip tip="Infected" left ><td>$$I$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="10" max="100000" step="1000" bind:value={I0} class="slider" id="myRange">
							</td>
							<td>{I0}</td>
						</tr>
						<tr>
							<SvelteTooltip tip="Asymptomatic infected" left ><td>$$A$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="10" max="100000" step="1000" bind:value={A0} class="slider" id="myRange">
							</td>
							<td>{A0}</td>
						</tr>

						<tr>
							<SvelteTooltip tip="Quarantined susceptible" left ><td>$$S_q$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="10" max="100000" step="1000" bind:value={Sq0} class="slider" id="myRange">
							</td>
							<td>{Sq0}</td>
						</tr>

						<tr>
							<SvelteTooltip tip="Quarantined exposed" left ><td>$$E_q$$</td></SvelteTooltip>
							<td class="slidecontainer">
							<input type="range" min="10" max="100000" step="1000" bind:value={Eq0} class="slider" id="myRange">
							</td>
							<td>{Eq0}</td>
						</tr>

						<tr>
						<SvelteTooltip tip="Hospitalized" left ><td>$$H$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="10" max="100000" step="1000" bind:value={H0} class="slider" id="myRange">
							</td>
							<td>{H0}</td>
						</tr>

						<tr>
						<SvelteTooltip tip="Recovered" left ><td>$$R$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="10" max="100000" step="1000" bind:value={R0} class="slider" id="myRange">
							</td>
							<td>{R0}</td>
						</tr>
		
					</tbody>
				</table>
	</div>
	</div>
	</div>
		
					<div class="panel-group col-md-6 p-2 "  style="display:inline-block;">
	<div class="panel panel-info">
	 <div class="panel-heading">Modify values of parameters!</div>
	 <div class="panel-body">

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
							<SvelteTooltip tip="Initial contact rate" left ><td>$$c_0$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="12.48" max="45" step="0.3252" bind:value={c0} class="slider" id="myRange">
							</td>
							<td>{c0}</td>
						</tr>

						<tr>
							<SvelteTooltip tip="Min. contact rate under control strategies" left ><td>$$c_a$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="1.0" max="6.0" step="0.05" bind:value={ca} class="slider" id="myRange">
							</td>
							<td>{ca}</td>
						</tr>

						<tr>
							<SvelteTooltip tip="Max. quarantined rate under control strategies" left ><td>$$q_1$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.00000000000015" max="0.000012" step="0.00000000005" bind:value={q1} class="slider" id="myRange">
							</td>
							<td>{q1}</td>
						</tr>

						<tr>
							<SvelteTooltip tip="Probability of transmission per contact" left ><td>$$\beta_0$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.00000000000015" max="0.000012" step="0.00000000005" bind:value={beta0} class="slider" id="myRange">
							</td>
							<td>{beta0}</td>
						</tr>

						<tr>
							<SvelteTooltip tip="Initial quarantined rate of exposed individuals" left ><td>$$q_0$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range"  min="0.000000000015" max="0.0012" step="0.000000005" bind:value={q0} class="slider" id="myRange">
							</td>
							<td>{q0}</td>
						</tr>

						<tr>
							<SvelteTooltip tip="Transition rate of exposed individuals to the infected class" left ><td>$$\sigma$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.0666" max="0.333" step="0.02" bind:value={sigma} class="slider" id="myRange">
							</td>
							<td>{sigma}</td>
						</tr>


						<tr>
							<SvelteTooltip tip="Rate at which the quarantined uninfects were released into the wider community" left ><td>$$\lambda$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.02" max="1" step="0.1" bind:value={lambda} class="slider" id="myRange">
							</td>
							<td>{lambda}</td>
						</tr>

				
						<tr>
							<SvelteTooltip tip="Transition rate of symptomatic infected individuals to the quarantined infected class" left ><td>$$\delta_I$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.01" max="0.95" step="0.0094" bind:value={deltaI} class="slider" id="myRange">
							</td>
							<td>{deltaI}</td>
						</tr>
						<tr>
							<SvelteTooltip tip="Transition rate of quarantined exposed individuals to the quarantined infected class" left ><td>$$\delta_q$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.01" max="0.4" step="0.0074" bind:value={deltaq} class="slider" id="myRange">
							</td>
							<td>{deltaq}</td>
						</tr>

						<tr>
							<SvelteTooltip tip="Recovery rate of symptomatic infected individuals" left ><td>$$\gamma_I$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.01" max="0.75" step="0.0074" bind:value={gammaI} class="slider" id="myRange">
							</td>
							<td>{gammaI}</td>
						</tr>

						<tr>
							<SvelteTooltip tip="Recovery rate of asymptomatic infected individuals" left ><td>$$\gamma_A$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.01" max="0.36" step="0.0035"  bind:value={gammaA} class="slider" id="myRange">
							</td>
							<td>{gammaA}</td>
						</tr>

						<tr>
							<SvelteTooltip tip="Gamma h" left ><td>$$\gamma_H$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.01" max="0.1" step="0.001" bind:value={gammaH} class="slider" id="myRange">
							</td>
							<td>{gammaH}</td>
						</tr>
						<tr>
						<SvelteTooltip tip="Rate at which recovered individuals move into pre-symptomatic class" left ><td>$$\gamma_R$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.0000003" max="0.00001" step="0.001" bind:value={gammaR} class="slider" id="myRange">
							</td>
							<td>{gammaR}</td>
						</tr>
						<tr>
						<SvelteTooltip tip="Relative transmission probability of A compared with I" left ><td>$$\theta$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.0000003" max="0.1" step="0.0001" bind:value={theta} class="slider" id="myRange">
							</td>
							<td>{theta}</td>
						</tr>

						<tr>
						<SvelteTooltip tip="Governmental action strength" left ><td>$$\alpha_b$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0" max="1" step="0.001" bind:value={alfab} class="slider" id="myRange">
							</td>
							<td>{alfab}</td>
						</tr>
						<tr>
						<SvelteTooltip tip="Alfa" left ><td>$$\alpha$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0" max="1" step="0.001" bind:value={alfa} class="slider" id="myRange">
							</td>
							<td>{alfa}</td>
						</tr>

						<tr>
						<SvelteTooltip tip="The intensity of the effect of tempreture variation" left ><td>$$\epsilon$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.001" max="0.9999" step="0.001" bind:value={eps} class="slider" id="myRange">
							</td>
							<td>{eps}</td>
						</tr>
						

					</tbody>
				</table>
	</div>
	</div>
	</div>
		

			
				


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
