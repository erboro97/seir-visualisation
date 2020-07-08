

<script>
	import rungeKutta from 'runge-kutta';
	import ode45 from 'ode45-cash-karp';
	import Chart from './Chart.svelte';
	import betaCh from './betaChart.svelte';
	import RRChart from './RR.svelte';
	import Cubic from './Cubic_Interpolation.svelte';
	import CubicTau from './Cubic_Interpolation_tau.svelte';
	import clone from 'clone';
	import Spline from 'cubic-spline';
	import SvelteTooltip from 'svelte-tooltip';

	let days=[];
	let ev=1;
	let files=[];


	/Parameter initial values/ 
	
	let S0=52740000.0;
	let		E0=19.220820541214668;
		let	I0=16.925617788469424;
	let		A0=17.99157876590649;
	let		Sq0=15.538827852131071;
	let		Eq0=1.7173986822830976;
	let		H0=46.0107243219873;
	let		R0=3.0748316104908433;
	let		c0=41.852549488391446;
	let		beta0=2.1031894440837713*Math.pow(10,-12);
	let		q0=1.484170130317585*Math.pow(10,-7);
	let		sigma=0.3333;
	let		lambda=0.071428;
	let		eps=0.88;
	let		deltaI=0.121;
	let		deltaq=0.03363;
	let		gammaI=0.1351146;
	let		gammaA=0.0117950;
	let		gammaH=0.028635;
	let		alfa=0.0021171;
	let		theta=0.00016357;
	let		c2=1.712150*Math.pow(10,-7);
	let		b=0.908;
	let		alfab=0.0012209;
	let		xi=0.1127126;
	let		gammaR=1.714578192*Math.pow(10,-6);
	let		ca=1.287039726
	let result=[];
	let tFunc=[]
	
	let pointsC=[];
	let pointsq=[];

	let cStep=10;

	let RRFunc=[];
	let betamFunc=[];
	let qFunc=[];
	let cFunc=[];
	let parameterSelection=[1,2];

	let data={};
	let countries = [
		{ id: 1, text: 'North-Italy' },
		{ id: 2, text: 'Hubei' }
	];
	let selectedCountry={};
	let tempreture=[6, 7, 7, 10, 10, 9, 8, 5, 4, 3, 7, 10, 8, 8, 3, 4, 6, 8, 10, 11, 9, 7, 7, 7, 6, 5, 4, 8, 9, 13, 11, 12, 13, 12, 14, 14, 7, 8, 10, 14, 11, 11, 16, 17, 17, 2, 7, 11, 12, 14, 15, 15, 17, 17, 22, 20, 17, 11, 9, 14, 15, 10, 9, 13, 14, 13, 17, 18, 13, 16, 17, 17, 16, 18, 20, 18, 20, 21, 22, 24, 22, 21, 22, 23, 23, 24, 10, 10, 9, 13, 13, 16, 17, 18, 19, 20, 20, 21, 22, 26, 17]
	let relativeHumidity=[68, 78, 82, 75, 88, 92, 78, 77, 89, 93, 77, 65, 68, 63, 84, 79, 69, 64, 54, 50, 77, 84, 87, 87, 83, 85, 74, 63, 63, 53, 51, 59, 62, 57, 59, 57, 81, 76, 68, 62, 69, 83, 76, 83, 82, 84, 50, 41, 44, 54, 55, 58, 59, 56, 64, 68, 78, 83, 86, 69, 65, 78, 77, 62, 60, 76, 73, 81, 82, 58, 52, 65, 67, 54, 43, 50, 57, 56, 51, 43, 71, 71, 65, 65, 77, 74, 78, 74, 83, 69, 71, 67, 66, 55, 53, 48, 48, 48, 49, 45, 70]

	$:{ 
		if (files){
			for (const file of files) {
    			// Not supported in Safari for iOS.
    			const reader = new FileReader();
  				reader.addEventListener('load', (event) => {
				let result= event.target.result;
				
				result = result.replace(/\\"/g,'"');
				if (result[0]=='"'){
					result = result.substring(1);
				result = result.substring(0, result.length - 1)
				}
				
				console.log(result);

				let jsonResult=JSON.parse(result);
				if (jsonResult.S0!=undefined && !isNaN(jsonResult.S0)){	
					
					S0=jsonResult.S0;
					console.log(jsonResult.S0)

				}
				if (jsonResult.E0!=undefined && !isNaN(jsonResult.E0))
					E0=jsonResult.E0
				if (jsonResult.I0!=undefined && !isNaN(jsonResult.I0))
					I0=jsonResult.I0
				if (jsonResult.A0!=undefined && !isNaN(jsonResult.A0))
					A0=jsonResult.A0
				if (jsonResult.Sq0!=undefined && !isNaN(jsonResult.Sq0))
					Sq0=jsonResult.Sq0
				if (jsonResult.Eq0!=undefined && !isNaN(jsonResult.Eq0))
					Eq0=jsonResult.Eq0
				if (jsonResult.H0!=undefined && !isNaN(jsonResult.H0))
					H0=jsonResult.H0
				if (jsonResult.R0!=undefined && !isNaN(jsonResult.R0))
					R0=jsonResult.R0
				if (jsonResult.c0!=undefined && !isNaN(jsonResult.c0))
					c0=jsonResult.c0
				if (jsonResult.ca!=undefined && !isNaN(jsonResult.ca))
					ca=jsonResult.ca
				if (jsonResult.eps!=undefined && !isNaN(jsonResult.eps))
					eps=jsonResult.eps
				if (jsonResult.beta0!=undefined && !isNaN(jsonResult.beta0))
					beta0=jsonResult.beta0
				if (jsonResult.q0!=undefined && !isNaN(jsonResult.q0))
					q0=jsonResult.q0
				if (jsonResult.sigma!=undefined && !isNaN(jsonResult.sigma))
					sigma=jsonResult.sigma
				if (jsonResult.lambda!=undefined && !isNaN(jsonResult.lambda))
					lambda=jsonResult.lambda
				if (jsonResult.deltaI!=undefined && !isNaN(jsonResult.deltaI))
					deltaI=jsonResult.deltaI
				if (jsonResult.deltaq!=undefined && !isNaN(jsonResult.deltaq))
					deltaq=jsonResult.deltaq
				if (jsonResult.gammaI!=undefined && !isNaN(jsonResult.gammaI))
					gammaI=jsonResult.gammaI
				if (jsonResult.gammaA!=undefined && !isNaN(jsonResult.gammaA))
					gammaA=jsonResult.gammaA
				if (jsonResult.gammaH!=undefined && !isNaN(jsonResult.gammaH))
					gammaH=jsonResult.gammaH
				if (jsonResult.gammaR!=undefined && !isNaN(jsonResult.gammaR))
					gammaR=jsonResult.gammaR
				if (jsonResult.theta!=undefined && !isNaN(jsonResult.theta))
					theta=jsonResult.theta
				if (jsonResult.alfab!=undefined && !isNaN(jsonResult.alfab))
					alfab=jsonResult.alfab
				if (jsonResult.alfa!=undefined && !isNaN(jsonResult.alfa))
					alfa=jsonResult.alfa
				if (jsonResult.xi!=undefined && !isNaN(jsonResult.xi))
					xi=jsonResult.xi
 				 });
				  reader.readAsText(file);
				files=[]

				  
	

  }
  }


		console.log(parameterSelection)
		if (selectedCountry.id==1){

	}
	if (selectedCountry.id==2){
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
	selectedCountry.id=0;
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
				 tempreture[i]=spline.at(i);
				 if (tempreture[i]==NaN && tempreture[i]==undefined){
					 tempreture[i]=0;
				 }
			}
		}
		if (Array.isArray(xCoordq) && xCoordq.length) {
			const spline = new Spline(xCoordq, yCoordq);
			for (let i = 0; i < 90; i++) {
 				relativeHumidity[i]=spline.at(i);
				 if (relativeHumidity[i]==NaN && relativeHumidity[i]==undefined){
					 relativeHumidity[i]=0;
				 }
			}
		}

	
		let func=function(t,y){
				tFunc.push(t)
				let S, E, I, A, Sq, Eq, H, R;
				let f0, f1, f2, f3, f4, f5, f6, f7;
				S = y[0];
    			E = y[1];
    			I = y[2];
    			A=y[3];
    			Sq=y[4];
    			Eq=y[5];
   				H=y[6];
				R = y[7];
				let betam;
				if (parameterSelection.includes(1)&&parameterSelection.includes(2)){
					let AH=6112 * Math.exp(17.67*tempreture[ Math.floor(t)]/(tempreture[Math.floor(t)] + 243.5)) * relativeHumidity[Math.floor(t)] * 2.1674 / (273.15 + relativeHumidity[Math.floor(t)]);
					betam=beta0*(1-alfab)*(1 + xi*AH) *  Math.pow((1- (I+A)/(S+R) ), 2);
				}
				else if (parameterSelection.includes(1)){
					betam=beta0*(1-alfab)*Math.exp(-xi*tempreture[ Math.floor(t)]) *  Math.pow((1- (I+A)/(S+R) ), 2);
				}
				else if (parameterSelection.includes(2)){
					betam=beta0*(1-alfab)*Math.exp(-xi*tempreture[ Math.floor(t)]) *  Math.pow((1- (I+A)/(S+R) ), 2);
				}
				
				betamFunc.push(betam);
				let ct=ca + (3*(c0-ca))/(1+2*Math.pow(b,-t));
				cFunc.push(ct);
				let qt=(t+q0/c2)/(t+1)*c2; 
				qFunc.push(qt);
				RRFunc.push((betam*eps*ct*(1-qt)/(deltaI+alfa+gammaI) + betam*ct*theta*(1-eps)*(1-qt)/gammaA ) * S)
				f0 =-(betam*ct+ct*qt*(1-betam))*S*(I+theta*A)+lambda*Sq;
    			f1 = betam*ct*(1-qt)*S*(I+theta*A)-sigma*E;
    			f2 = sigma*eps*E-(deltaI+alfa+gammaI)*I;
    			f3= sigma*(1-eps)*E-gammaA*A + gammaR*R;
    			f4= (1-betam)*ct*qt*S*(I+theta*A)-lambda*Sq;
    			f5=betam*ct*qt*S*(I+theta*A)-deltaq*Eq;
    			f6=deltaI*I+deltaq*Eq-(alfa+gammaH)*H;
				f7=gammaI*I+gammaA*A+gammaH*H - gammaR*R;
				
				return [f0,f1,f2,f3,f4,f5,f6,f7]
				

		}

			// Initialize:
			let y0 = [S0, E0,I0,A0,Sq0,Eq0,H0,R0];
	
			let y=rungeKutta(func, y0, [0, 99], 1);
			days=[]
			for (let i=0;i<100;i++){
				days.push(i);
			}
			result=[tFunc,y,days];

			


			data.RR=RRFunc;
			data.beta=betamFunc;
			data.c=cFunc;
			data.q=qFunc;
			data.days=days;

			
				
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

function handleClick() {
	let result="{ 'S0':"+S0+", 'E0':"+E0+", 'I0':"+I0+", 'A0':"+A0+", 'Sq0':"+Sq0+", 'Eq0':"+Eq0+", 'H0':"+H0+ ", 'R0':"+R0+", 'c0':"+c0+", 'ca':"+ca+", 'eps':"+eps+", 'beta0':"+beta0+", 'q0':"+q0+", 'sigma':"+sigma+", 'lambda':"+lambda+", 'deltaI':"+deltaI+", 'deltaq':"+deltaq+", 'gammaI':"+gammaI+", 'gammaH':"+gammaH+", 'gammaR':"+gammaR+", 'theta':"+theta+", 'alfab':"+alfab+", 'alfa':"+alfa+", 'xi':"+xi+"}";
	 result=result.split("'").join('"');
	 let dataStr = JSON.stringify(result);
    let dataUri = 'data:application/json;charset=utf-8,'+ encodeURIComponent(dataStr);
	dataStr = dataStr.replace(/\\"/g,'"');
	console.log(dataStr);
    let exportFileDefaultName = 'data.json';

    let linkElement = document.createElement('a');
    linkElement.setAttribute('href', dataUri);
    linkElement.setAttribute('download', exportFileDefaultName);
    linkElement.click();
	}



</script>



<main>
	<div class="container">
	

	<div class="panel-group "  >
	<div class="panel panel-success col-md-8 p-2" style="display:inline-block;" >
	 <div class="panel-heading">Data visualization</div>
	 <div class="panel-body">
				<Chart chartData={result} />

	</div>
	</div>
	</div>

		<div class="panel-group col-md-4 p-2 "  style="display:inline-block;">
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
								<input type="range" min="10000" max="800000000" step="800000" bind:value={S0} class="slider" id="myRange">
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

	<div class="panel-group col-md-4 p-2 "  style="display:inline-block;">
	<div class="panel panel-success">
	 <div class="panel-heading">Select which factors to be included in the model</div>
	 <div class="panel-body">
<label><input type="checkbox" bind:group={parameterSelection} value={1} /> Humidity</label>
	<label><input type="checkbox" bind:group={parameterSelection} value={2} /> Tempreture</label>

	{#if parameterSelection.length === 0}
	<p>Please select at least one parameter</p>
{/if}
	</div>
	</div>
	</div>


	<div class="panel-group  col-md-4 p-2 " style="display:inline-block;">
			<div class="panel panel-danger" >
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
	{#if parameterSelection.includes(2)}
			<div class="panel-group col-md-5 p-2 "  style="display:inline-block;">
	<div class="panel panel-warning">
	 <div class="panel-heading">Select values of tempreture!</div>
	 <div class="panel-body">
		<Cubic temp={tempreture} on:myClick={myClickC}/>
	</div>
	</div>
	</div>
	{/if}
		
{#if parameterSelection.includes(1)}
		<div class="panel-group col-md-5 p-2 "  style="display:inline-block;">
	<div class="panel panel-warning">
	 <div class="panel-heading">Select values of absolute humid!</div>
	 <div class="panel-body">
				<CubicTau humidity={relativeHumidity} on:myClick={myClickTau}/>

	</div>
	</div>
	</div>

	
	
		
	{/if}


	
<div class="panel-group col-md-8 p-2 "  style="display:inline-block;">
	<div class="panel panel-success">
	 <div class="panel-heading">Data visualization</div>
	 <div class="panel-body">
		<RRChart data={data}/>

	</div>
	</div>
	</div>
			

			
					<div class="panel-group col-md-4 p-2 "  style="display:inline-block;">
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
								<input type="range" min="0.00000000000015" max="0.000012" step="0.00000000005" bind:value={q0} class="slider" id="myRange">
							</td>
							<td>{q0}</td>
						</tr>

						<tr>
							<SvelteTooltip tip="Probability of transmission per contact" left ><td>$$\beta_0$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.00000000000015" max="0.000012" step="0.00000000005" bind:value={beta0} class="slider" id="myRange">
							</td>
							<td>{beta0}</td>
						</tr>

						<tr>
							<SvelteTooltip tip="Initial quarantined rate of exposed individuals" left ><td>$$\epsilon$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range"  min="0.000000000015" max="0.0012" step="0.000000005" bind:value={eps} class="slider" id="myRange">
							</td>
							<td>{eps}</td>
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

		<div class="panel-group col-md-6 p-2 "  style="display:inline-block;">
	<div class="panel panel-warning">
	 <div class="panel-heading">Information about model</div>
	 <div class="panel-body">
	 <SvelteTooltip tip=$$w()$$ left ><td>$$S'=-w(S,I,A)+\lambda S_q$$
			$$E'=f(S,I,A)-\sigma E$$
			$$I'=\sigma\varrho E-\left(\delta_I+\alpha+\gamma_I\right)I$$
			$$A'=\sigma\left(1-\varrho\right)E-\gamma_A A+\gamma_R R$$
			$$S_q'=g(S,I,A)-\lambda S_q$$
			$$E_q'=h(S,I,A)-S_q E_q$$
			$$H'=\delta_I I+\delta_q E_q -\left(\alpha+\gamma_H \right)H$$
			$$R'=\gamma_I I+\gamma_A A+\gamma_H H-\gamma_R R$$</td></SvelteTooltip>
</div>
	</div>
	</div>

	<input bind:files type='file' >
	<button on:click={handleClick}>
	Export
	</button>


	
		

		
	

		
			
		


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
