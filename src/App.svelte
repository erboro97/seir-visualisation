

<script>
	import rungeKutta from 'runge-kutta';
	import ode45 from 'ode45-cash-karp';
	import Chart from './Chart.svelte';
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
	let countriesPopulation=
[
	{
        "country": "Hubei",
        "population": 52740000
    },
    {
        "country": "Afghanistan",
        "population": 37172386
    },
    {
        "country": "Albania",
        "population": 2866376
    },
    {
        "country": "Algeria",
        "population": 42228429
    },
    {
        "country": "American Samoa",
        "population": 55465
    },
    {
        "country": "Andorra",
        "population": 77006
    },
    {
        "country": "Angola",
        "population": 30809762
    },
    {
        "country": "Anguilla",
        "population": 15094
    },
    {
        "country": "Antarctica",
        "population": 1106
    },
    {
        "country": "Antigua and Barbuda",
        "population": 96286
    },
    {
        "country": "Argentina",
        "population": 44494502
    },
    {
        "country": "Armenia",
        "population": 2951776
    },
    {
        "country": "Aruba",
        "population": 105845
    },
    {
        "country": "Australia",
        "population": 24982688
    },
    {
        "country": "Austria",
        "population": 8840521
    },
    {
        "country": "Azerbaijan",
        "population": 9939800
    },
    {
        "country": "Bahamas",
        "population": 385640
    },
    {
        "country": "Bahrain",
        "population": 1569439
    },
    {
        "country": "Bangladesh",
        "population": 161356039
    },
    {
        "country": "Barbados",
        "population": 286641
    },
    {
        "country": "Belarus",
        "population": 9483499
    },
    {
        "country": "Belgium",
        "population": 11433256
    },
    {
        "country": "Belize",
        "population": 383071
    },
    {
        "country": "Benin",
        "population": 11485048
    },
    {
        "country": "Bermuda",
        "population": 63973
    },
    {
        "country": "Bhutan",
        "population": 754394
    },
    {
        "country": "Bolivia",
        "population": 11353142
    },
    {
        "country": "Bosnia and Herzegovina",
        "population": 3323929
    },
    {
        "country": "Botswana",
        "population": 2254126
    },
    {
        "country": "Bouvet Island",
        "population": 0
    },
    {
        "country": "Brazil",
        "population": 209469333
    },
    {
        "country": "British Indian Ocean Territory",
        "population": 0
    },
    {
        "country": "Brunei",
        "population": 428962
    },
    {
        "country": "Bulgaria",
        "population": 7025037
    },
    {
        "country": "Burkina Faso",
        "population": 19751535
    },
    {
        "country": "Burundi",
        "population": 11175378
    },
    {
        "country": "Cambodia",
        "population": 16249798
    },
    {
        "country": "Cameroon",
        "population": 25216237
    },
    {
        "country": "Canada",
        "population": 37057765
    },
    {
        "country": "Cape Verde",
        "population": 543767
    },
    {
        "country": "Cayman Islands",
        "population": 64174
    },
    {
        "country": "Central African Republic",
        "population": 4666377
    },
    {
        "country": "Chad",
        "population": 15477751
    },
    {
        "country": "Chile",
        "population": 18729160
    },
    {
        "country": "China",
        "population": 1392730000
    },
    {
        "country": "Christmas Island",
        "population": 1402
    },
    {
        "country": "Cocos (Keeling) Islands",
        "population": 596
    },
    {
        "country": "Colombia",
        "population": 49648685
    },
    {
        "country": "Comoros",
        "population": 832322
    },
    {
        "country": "Congo",
        "population": 5244363
    },
    {
        "country": "Cook Islands",
        "population": 17379
    },
    {
        "country": "Costa Rica",
        "population": 4999441
    },
    {
        "country": "Croatia",
        "population": 4087843
    },
    {
        "country": "Cuba",
        "population": 11338138
    },
    {
        "country": "Cyprus",
        "population": 1189265
    },
    {
        "country": "Czech Republic",
        "population": 10629928
    },
    {
        "country": "Denmark",
        "population": 5793636
    },
    {
        "country": "Djibouti",
        "population": 958920
    },
    {
        "country": "Dominica",
        "population": 71625
    },
    {
        "country": "Dominican Republic",
        "population": 10627165
    },
    {
        "country": "East Timor",
        "population": 1267972
    },
    {
        "country": "Ecuador",
        "population": 17084357
    },
    {
        "country": "Egypt",
        "population": 98423595
    },
    {
        "country": "El Salvador",
        "population": 6420744
    },
    {
        "country": "England",
        "population": 55619400
    },
    {
        "country": "Equatorial Guinea",
        "population": 1308974
    },
    {
        "country": "Eritrea",
        "population": 3213972
    },
    {
        "country": "Estonia",
        "population": 1321977
    },
    {
        "country": "Ethiopia",
        "population": 109224559
    },
    {
        "country": "Falkland Islands",
        "population": 2840
    },
    {
        "country": "Faroe Islands",
        "population": 48497
    },
    {
        "country": "Fiji Islands",
        "population": 883483
    },
    {
        "country": "Finland",
        "population": 5515525
    },
    {
        "country": "France",
        "population": 66977107
    },
    {
        "country": "French Guiana",
        "population": 290691
    },
    {
        "country": "French Polynesia",
        "population": 277679
    },
    {
        "country": "French Southern territories",
        "population": 0
    },
    {
        "country": "Gabon",
        "population": 2119275
    },
    {
        "country": "Gambia",
        "population": 2280102
    },
    {
        "country": "Georgia",
        "population": 3726549
    },
    {
        "country": "Germany",
        "population": 82905782
    },
    {
        "country": "Ghana",
        "population": 29767108
    },
    {
        "country": "Gibraltar",
        "population": 33718
    },
    {
        "country": "Greece",
        "population": 10731726
    },
    {
        "country": "Greenland",
        "population": 56025
    },
    {
        "country": "Grenada",
        "population": 111454
    },
    {
        "country": "Guadeloupe",
        "population": 395700
    },
    {
        "country": "Guam",
        "population": 165768
    },
    {
        "country": "Guatemala",
        "population": 17247807
    },
    {
        "country": "Guinea",
        "population": 12414318
    },
    {
        "country": "Guinea-Bissau",
        "population": 1874309
    },
    {
        "country": "Guyana",
        "population": 779004
    },
    {
        "country": "Haiti",
        "population": 11123176
    },
    {
        "country": "Heard Island and McDonald Islands",
        "population": 0
    },
    {
        "country": "Holy See (Vatican City State)",
        "population": 825
    },
    {
        "country": "Honduras",
        "population": 9587522
    },
    {
        "country": "Hong Kong",
        "population": 7451000
    },
    {
        "country": "Hungary",
        "population": 9775564
    },
    {
        "country": "Iceland",
        "population": 352721
    },
    {
        "country": "India",
        "population": 1352617328
    },
    {
        "country": "Indonesia",
        "population": 267663435
    },
    {
        "country": "Iran",
        "population": 81800269
    },
    {
        "country": "Iraq",
        "population": 38433600
    },
    {
        "country": "Ireland",
        "population": 4867309
    },
    {
        "country": "Israel",
        "population": 8882800
    },
    {
        "country": "Italy",
        "population": 60421760
    },
    {
        "country": "Ivory Coast",
        "population": 25069229
    },
    {
        "country": "Jamaica",
        "population": 2934855
    },
    {
        "country": "Japan",
        "population": 126529100
    },
    {
        "country": "Jordan",
        "population": 9956011
    },
    {
        "country": "Kazakhstan",
        "population": 18272430
    },
    {
        "country": "Kenya",
        "population": 51393010
    },
    {
        "country": "Kiribati",
        "population": 115847
    },
    {
        "country": "Kuwait",
        "population": 4137309
    },
    {
        "country": "Kyrgyzstan",
        "population": 6322800
    },
    {
        "country": "Laos",
        "population": 7061507
    },
    {
        "country": "Latvia",
        "population": 1927174
    },
    {
        "country": "Lebanon",
        "population": 6848925
    },
    {
        "country": "Lesotho",
        "population": 2108132
    },
    {
        "country": "Liberia",
        "population": 4818977
    },
    {
        "country": "Libyan Arab Jamahiriya",
        "population": 6678567
    },
    {
        "country": "Liechtenstein",
        "population": 37910
    },
    {
        "country": "Lithuania",
        "population": 2801543
    },
    {
        "country": "Luxembourg",
        "population": 607950
    },
    {
        "country": "Macao",
        "population": 631636
    },
    {
        "country": "North Macedonia",
        "population": 2084367
    },
    {
        "country": "Madagascar",
        "population": 26262368
    },
    {
        "country": "Malawi",
        "population": 18143315
    },
    {
        "country": "Malaysia",
        "population": 31528585
    },
    {
        "country": "Maldives",
        "population": 515696
    },
    {
        "country": "Mali",
        "population": 19077690
    },
    {
        "country": "Malta",
        "population": 484630
    },
    {
        "country": "Marshall Islands",
        "population": 58413
    },
    {
        "country": "Martinique",
        "population": 376480
    },
    {
        "country": "Mauritania",
        "population": 4403319
    },
    {
        "country": "Mauritius",
        "population": 1265303
    },
    {
        "country": "Mayotte",
        "population": 270372
    },
    {
        "country": "Mexico",
        "population": 126190788
    },
    {
        "country": "Micronesia, Federated States of",
        "population": 112640
    },
    {
        "country": "Moldova",
        "population": 2706049
    },
    {
        "country": "Monaco",
        "population": 38682
    },
    {
        "country": "Mongolia",
        "population": 3170208
    },
    {
        "country": "Montenegro",
        "population": 631219
    },
    {
        "country": "Montserrat",
        "population": 5900
    },
    {
        "country": "Morocco",
        "population": 36029138
    },
    {
        "country": "Mozambique",
        "population": 29495962
    },
    {
        "country": "Myanmar",
        "population": 53708395
    },
    {
        "country": "Namibia",
        "population": 2448255
    },
    {
        "country": "Nauru",
        "population": 12704
    },
    {
        "country": "Nepal",
        "population": 28087871
    },
    {
        "country": "Netherlands",
        "population": 17231624
    },
    {
        "country": "Netherlands Antilles",
        "population": 227049
    },
    {
        "country": "New Caledonia",
        "population": 284060
    },
    {
        "country": "New Zealand",
        "population": 4841000
    },
    {
        "country": "Nicaragua",
        "population": 6465513
    },
    {
        "country": "Niger",
        "population": 22442948
    },
    {
        "country": "Nigeria",
        "population": 195874740
    },
    {
        "country": "Niue",
        "population": 1624
    },
    {
        "country": "Norfolk Island",
        "population": 2169
    },
    {
        "country": "North Korea",
        "population": 25549819
    },
    {
        "country": "Northern Ireland",
        "population": 1885400
    },
    {
        "country": "Northern Mariana Islands",
        "population": 56882
    },
    {
        "country": "Norway",
        "population": 5311916
    },
    {
        "country": "Oman",
        "population": 4829483
    },
    {
        "country": "Pakistan",
        "population": 212215030
    },
    {
        "country": "Palau",
        "population": 17907
    },
    {
        "country": "Palestine",
        "population": 4569087
    },
    {
        "country": "Panama",
        "population": 4176873
    },
    {
        "country": "Papua New Guinea",
        "population": 8606316
    },
    {
        "country": "Paraguay",
        "population": 6956071
    },
    {
        "country": "Peru",
        "population": 31989256
    },
    {
        "country": "Philippines",
        "population": 106651922
    },
    {
        "country": "Pitcairn",
        "population": 67
    },
    {
        "country": "Poland",
        "population": 37974750
    },
    {
        "country": "Portugal",
        "population": 10283822
    },
    {
        "country": "Puerto Rico",
        "population": 3195153
    },
    {
        "country": "Qatar",
        "population": 2781677
    },
    {
        "country": "Reunion",
        "population": 859959
    },
    {
        "country": "Romania",
        "population": 19466145
    },
    {
        "country": "Russian Federation",
        "population": 144478050
    },
    {
        "country": "Rwanda",
        "population": 12301939
    },
    {
        "country": "Saint Helena",
        "population": 6600
    },
    {
        "country": "Saint Kitts and Nevis",
        "population": 52441
    },
    {
        "country": "Saint Lucia",
        "population": 181889
    },
    {
        "country": "Saint Pierre and Miquelon",
        "population": 5888
    },
    {
        "country": "Saint Vincent and the Grenadines",
        "population": 110210
    },
    {
        "country": "Samoa",
        "population": 196130
    },
    {
        "country": "San Marino",
        "population": 33785
    },
    {
        "country": "Sao Tome and Principe",
        "population": 211028
    },
    {
        "country": "Saudi Arabia",
        "population": 33699947
    },
    {
        "country": "Scotland",
        "population": 5424800
    },
    {
        "country": "Senegal",
        "population": 15854360
    },
    {
        "country": "Seychelles",
        "population": 96762
    },
    {
        "country": "Sierra Leone",
        "population": 7650154
    },
    {
        "country": "Singapore",
        "population": 5638676
    },
    {
        "country": "Slovakia",
        "population": 5446771
    },
    {
        "country": "Slovenia",
        "population": 2073894
    },
    {
        "country": "Solomon Islands",
        "population": 652858
    },
    {
        "country": "Somalia",
        "population": 15008154
    },
    {
        "country": "South Africa",
        "population": 57779622
    },
    {
        "country": "South Georgia and the South Sandwich Islands",
        "population": 30
    },
    {
        "country": "South Korea",
        "population": 51606633
    },
    {
        "country": "South Sudan",
        "population": 10975920
    },
    {
        "country": "Spain",
        "population": 46796540
    },
    {
        "country": "SriLanka",
        "population": 21670000
    },
    {
        "country": "Sudan",
        "population": 41801533
    },
    {
        "country": "Suriname",
        "population": 575991
    },
    {
        "country": "Svalbard and Jan Mayen",
        "population": 2572
    },
    {
        "country": "Swaziland",
        "population": 1136191
    },
    {
        "country": "Sweden",
        "population": 10175214
    },
    {
        "country": "Switzerland",
        "population": 8513227
    },
    {
        "country": "Syria",
        "population": 16906283
    },
    {
        "country": "Tajikistan",
        "population": 9100837
    },
    {
        "country": "Tanzania",
        "population": 56318348
    },
    {
        "country": "Thailand",
        "population": 69428524
    },
    {
        "country": "The Democratic Republic of Congo",
        "population": 84068091
    },
    {
        "country": "Togo",
        "population": 7889094
    },
    {
        "country": "Tokelau",
        "population": 1411
    },
    {
        "country": "Tonga",
        "population": 103197
    },
    {
        "country": "Trinidad and Tobago",
        "population": 1389858
    },
    {
        "country": "Tunisia",
        "population": 11565204
    },
    {
        "country": "Turkey",
        "population": 82319724
    },
    {
        "country": "Turkmenistan",
        "population": 5850908
    },
    {
        "country": "Turks and Caicos Islands",
        "population": 37665
    },
    {
        "country": "Tuvalu",
        "population": 11508
    },
    {
        "country": "Uganda",
        "population": 42723139
    },
    {
        "country": "Ukraine",
        "population": 44622516
    },
    {
        "country": "United Arab Emirates",
        "population": 9630959
    },
    {
        "country": "United Kingdom",
        "population": 66460344
    },
    {
        "country": "United States",
        "population": 326687501
    },
    {
        "country": "United States Minor Outlying Islands",
        "population": 300
    },
    {
        "country": "Uruguay",
        "population": 3449299
    },
    {
        "country": "Uzbekistan",
        "population": 32955400
    },
    {
        "country": "Vanuatu",
        "population": 292680
    },
    {
        "country": "Venezuela",
        "population": 28870195
    },
    {
        "country": "Vietnam",
        "population": 95540395
    },
    {
        "country": "Virgin Islands, British",
        "population": 29802
    },
    {
        "country": "Virgin Islands, U.S.",
        "population": 106977
    },
    {
        "country": "Wales",
        "population": 3139000
    },
    {
        "country": "Wallis and Futuna",
        "population": 15289
    },
    {
        "country": "Western Sahara",
        "population": 652271
    },
    {
        "country": "Yemen",
        "population": 28498687
    },
    {
        "country": "Yugoslavia",
        "population": null
    },
    {
        "country": "Zambia",
        "population": 17351822
    },
    {
        "country": "Zimbabwe",
        "population": 14439018
    }
];
	let parameterNumber=17;
	let parametersIncluded=Array.apply(null, Array(parameterNumber)).map(Number.prototype.valueOf,1);
	let parameterSelection=Array.from(Array(parameterNumber+2).keys())
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
	let		c01=41.852549488391446;
	let		beta01=2.1031894440837713*Math.pow(10,-12);
	let		q01=1.484170130317585*Math.pow(10,-7);
	let		sigma1=0.3333;
	let		lambda1=0.071428;
	let		eps1=0.88;
	let		deltaI1=0.121;
	let		deltaq1=0.03363;
	let		gammaI1=0.1351146;
	let		gammaA1=0.0117950;
	let		gammaH1=0.028635;
	let		alfa1=0.0021171;
	let		theta1=0.00016357;
	let		c21=1.712150*Math.pow(10,-7);
	let		b1=0.908;
	let		alfab1=0.0012209;
	let		xi1=0.1127126;
	let		gammaR1=1.714578192*Math.pow(10,-6);
	let		ca1=1.287039726
	let result=[];
	let tFunc=[]
	
	let pointsC=[];
	let pointsq=[];

	let cStep=10;

	let RRFunc=[];
	let betamFunc=[];
	let qFunc=[];
	let cFunc=[];
	

	let data={};
	let countries = [
		{ id: 1, text: 'North-Italy' },
		{ id: 2, text: 'Hubei' }
	];
	let selectedCountry={};
	let tempreture=[6, 7, 7, 10, 10, 9, 8, 5, 4, 3, 7, 10, 8, 8, 3, 4, 6, 8, 10, 11, 9, 7, 7, 7, 6, 5, 4, 8, 9, 13, 11, 12, 13, 12, 14, 14, 7, 8, 10, 14, 11, 11, 16, 17, 17, 2, 7, 11, 12, 14, 15, 15, 17, 17, 22, 20, 17, 11, 9, 14, 15, 10, 9, 13, 14, 13, 17, 18, 13, 16, 17, 17, 16, 18, 20, 18, 20, 21, 22, 24, 22, 21, 22, 23, 23, 24, 10, 10, 9, 13, 13, 16, 17, 18, 19, 20, 20, 21, 22, 26, 17]
	let relativeHumidity=[68, 78, 82, 75, 88, 92, 78, 77, 89, 93, 77, 65, 68, 63, 84, 79, 69, 64, 54, 50, 77, 84, 87, 87, 83, 85, 74, 63, 63, 53, 51, 59, 62, 57, 59, 57, 81, 76, 68, 62, 69, 83, 76, 83, 82, 84, 50, 41, 44, 54, 55, 58, 59, 56, 64, 68, 78, 83, 86, 69, 65, 78, 77, 62, 60, 76, 73, 81, 82, 58, 52, 65, 67, 54, 43, 50, 57, 56, 51, 43, 71, 71, 65, 65, 77, 74, 78, 74, 83, 69, 71, 67, 66, 55, 53, 48, 48, 48, 49, 45, 70]

	$:{ 

		for ( let i=0;i<parameterNumber;i++){
			if (!parameterSelection.includes(i)){
				parametersIncluded[i]=0;
			}
			else{
				parametersIncluded[i]=1;
			}
		}

		c01=Math.pow(c0,parametersIncluded[0]);
		ca1=Math.pow(ca,parametersIncluded[1]);
		q01=Math.pow(q0,parametersIncluded[2]);
		beta01=Math.pow(beta0,parametersIncluded[3]);
		eps1=Math.pow(eps,parametersIncluded[4]);
		sigma1=Math.pow(sigma,parametersIncluded[5]);
		lambda1=Math.pow(lambda,parametersIncluded[6]);
		deltaI1=Math.pow(deltaI,parametersIncluded[7]);
		deltaq1=Math.pow(deltaq,parametersIncluded[8]);
		gammaI1=Math.pow(gammaI,parametersIncluded[9]);
		gammaA1=Math.pow(gammaA,parametersIncluded[10]);
		gammaH1=Math.pow(gammaH,parametersIncluded[11]);
		gammaR1=Math.pow(gammaR,parametersIncluded[12]);
		theta1=Math.pow(theta,parametersIncluded[13]);
		alfab1=Math.pow(alfab,parametersIncluded[14]);
		alfa1=Math.pow(alfa,parametersIncluded[15]);
		xi1=Math.pow(xi,parametersIncluded[16]);

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
				
				let jsonResult=JSON.parse(result);
				if (jsonResult.S0!=undefined && !isNaN(jsonResult.S0)){	
					console.log("bement")
					S0=jsonResult.S0;

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
				if (jsonResult.alphab!=undefined && !isNaN(jsonResult.alphab))
					alfab=jsonResult.alphab
				if (jsonResult.alpha!=undefined && !isNaN(jsonResult.alpha))
					alfa=jsonResult.alpha
				if (jsonResult.xi!=undefined && !isNaN(jsonResult.xi))
					xi=jsonResult.xi
				if (jsonResult.c2!=undefined && !isNaN(jsonResult.c2))
					c2=jsonResult.c2
 				 });
				  reader.readAsText(file);
				files=[]

				  
	

  }
  }


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
	
		RRFunc=[];
		betamFunc=[];
		qFunc=[];
		cFunc=[];
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
				if (parameterSelection.includes(17)&&parameterSelection.includes(18)){
					let AH=6112 * Math.exp(17.67*tempreture[ Math.floor(t)]/(tempreture[Math.floor(t)] + 243.5)) * relativeHumidity[Math.floor(t)] * 2.1674 / (273.15 + relativeHumidity[Math.floor(t)]);
					betam=beta0*(1-alfab)*(1 + xi*AH) *  Math.pow((1- (I+A)/(S+R) ), 2);
				}
				else if (parameterSelection.includes(17)){
					betam=beta0*(1-alfab)*Math.exp(-xi*tempreture[ Math.floor(t)]) *  Math.pow((1- (I+A)/(S+R) ), 2);
				}
				else if (parameterSelection.includes(18)){
					betam=beta0*(1-alfab)*Math.exp(-xi*tempreture[ Math.floor(t)]) *  Math.pow((1- (I+A)/(S+R) ), 2);
				}
				
				betamFunc.push(betam);
				let ct=ca1 + (3*(c01-ca1))/(1+2*Math.pow(b1,-t));
				cFunc.push(ct);
				let qt=(t+q01/c21)/(t+1)*c21; 
				qFunc.push(qt);
				RRFunc.push((betam*eps1*ct*(1-qt)/(deltaI1+alfa1+gammaI1) + betam*ct*theta1*(1-eps1)*(1-qt)/gammaA1 ) * S)
				f0 =-(betam*ct+ct*qt*(1-betam))*S*(I+theta1*A)+lambda1*Sq;
    			f1 = betam*ct*(1-qt)*S*(I+theta1*A)-sigma1*E;
    			f2 = sigma1*eps1*E-(deltaI1+alfa1+gammaI1)*I;
    			f3= sigma1*(1-eps1)*E-gammaA1*A + gammaR1*R;
    			f4= (1-betam)*ct*qt*S*(I+theta1*A)-lambda1*Sq;
    			f5=betam*ct*qt*S*(I+theta1*A)-deltaq1*Eq;
    			f6=deltaI1*I+deltaq1*Eq-(alfa1+gammaH1)*H;
				f7=gammaI1*I+gammaA1*A+gammaH1*H - gammaR1*R;

				
				
				return [f0,f1,f2,f3,f4,f5,f6,f7]
				

		}

			// Initialize:
			let y0 = [S0, E0,I0,A0,Sq0,Eq0,H0,R0];
	
			let y=rungeKutta(func, y0, [0, 99], 0.1);
			days=[]
			for (let i=0;i<100;i++){
				days.push(i);
			}
			
			let yBiggerStep=[], k=0, RRBiggerStep=[], betaBiggerStep=[], cBiggerStep=[], qBiggerStep=[];
			for (let i=0;i<y.length;i+=9){
				yBiggerStep[k]=y[i];
				k++
			}
			result=[tFunc,yBiggerStep,days];
			k=0;
			RRBiggerStep=[]
			let newElement;
			for (let i=0;i<RRFunc.length;i+=40){
				newElement=clone(RRFunc[i]);
				RRBiggerStep[k]=RRFunc[i];
				k++;
			}
			k=0;

			for (let i=0;i<betamFunc.length;i+=40){
				betaBiggerStep[k]=betamFunc[i];
				k++
			}
			k=0;
			for (let i=0;i<cFunc.length;i+=40){
				cBiggerStep[k]=cFunc[i];
				k++
			}
			k=0;
			for (let i=0;i<qFunc.length;i+=40){
				qBiggerStep[k]=qFunc[i];
				k++
			}
			data.RR=RRBiggerStep;

			data.beta=betaBiggerStep;
			data.c=cBiggerStep;
			data.q=qBiggerStep;
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
	let result="{ 'S0':"+S0+", 'E0':"+E0+", 'I0':"+I0+", 'A0':"+A0+", 'Sq0':"+Sq0+", 'Eq0':"+Eq0+", 'H0':"+H0+ ", 'R0':"+R0+", 'c0':"+c0+", 'ca':"+ca+", 'eps':"+eps+", 'beta0':"+beta0+", 'q0':"+q0+", 'sigma':"+sigma+", 'lambda':"+lambda+", 'deltaI':"+deltaI+", 'deltaq':"+deltaq+", 'gammaI':"+gammaI+", 'gammaH':"+gammaH+", 'gammaR':"+gammaR+", 'theta':"+theta+", 'alphab':"+alfab+", 'alpha':"+alfa+", 'xi':"+xi+", 'c2':"+c2+"}";
	 result=result.split("'").join('"');
	 let dataStr = JSON.stringify(result);
    let dataUri = 'data:application/json;charset=utf-8,'+ encodeURIComponent(dataStr);
	dataStr = dataStr.replace(/\\"/g,'"');
    let exportFileDefaultName = 'data.json';

    let linkElement = document.createElement('a');
    linkElement.setAttribute('href', dataUri);
    linkElement.setAttribute('download', exportFileDefaultName);
    linkElement.click();
	}



</script>



<main>
	<div class="container">
	

	<div class="panel-group col-md-8 p-6"  style="display:inline-block;">
	<div class="panel panel-primary "  >
	 <div class="panel-heading"><strong>Data visualization </strong></div>
	 <div class="panel-body">
				<Chart chartData={result} />

	</div>
	</div>
	</div>

		<div class="panel-group col-md-4 p-2 "  style="display:inline-block;">
	<div class="panel panel-info">
	 <div class="panel-heading"><strong>Set initial conditions</strong></div>
	 <div class="panel-body">
<table class="table table-bordered ">
					<thead>
						<tr>
							<th class="small">Definition:</th>
							<th class="small">Range of value:</th>
							<th class="small">Selected value:</th>
						</tr>
					</thead>
					<tbody>
						<tr>
	
							<SvelteTooltip tip="Susceptible" class="alert alert-info" left color="#a0daa2"><td>$$S$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="10000" max="800000000" step="800000" bind:value={S0} class="slider" id="myRange">
							</td>
							<td>{S0}</td>
						</tr>
						<tr>
							<SvelteTooltip tip="Exposed" left color="#a0daa2" ><td>$$E$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="10" max="1000000" step="10000" bind:value={E0} class="slider" id="myRange">
							</td>
							<td>{E0.toFixed(2)}</td>
						</tr>
						<tr>
							<SvelteTooltip tip="Infected" left color="#a0daa2"><td>$$I$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="10" max="100000" step="1000" bind:value={I0} class="slider" id="myRange">
							</td>
							<td>{I0.toFixed(2)}</td>
						</tr>
						<tr>
							<SvelteTooltip tip="Asymptomatic infected" left color="#a0daa2"><td>$$A$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="10" max="100000" step="1000" bind:value={A0} class="slider" id="myRange">
							</td>
							<td>{A0.toFixed(2)}</td>
						</tr>

						<tr>
							<SvelteTooltip tip="Quarantined susceptible" left color="#a0daa2"><td>$$S_q$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="10" max="100000" step="1000" bind:value={Sq0} class="slider" id="myRange">
							</td>
							<td>{Sq0.toFixed(2)}</td>
						</tr>

						<tr>
							<SvelteTooltip tip="Quarantined exposed" left color="#a0daa2"><td>$$E_q$$</td></SvelteTooltip>
							<td class="slidecontainer">
							<input type="range" min="10" max="100000" step="1000" bind:value={Eq0} class="slider" id="myRange">
							</td>
							<td>{Eq0.toFixed(2)}</td>
						</tr>

						<tr>
						<SvelteTooltip tip="Hospitalized" left color="#a0daa2"><td>$$H$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="10" max="100000" step="1000" bind:value={H0} class="slider" id="myRange">
							</td>
							<td>{H0.toFixed(2)}</td>
						</tr>

						<tr>
						<SvelteTooltip tip="Recovered" left color="#a0daa2"><td>$$R$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="10" max="100000" step="1000" bind:value={R0} class="slider" id="myRange">
							</td>
							<td>{R0.toFixed(2)}</td>
						</tr>
		
					</tbody>
				</table>
	</div>
	</div>
	</div>

		<div class="panel-group  col-md-4 p-2 " style="display:inline-block;">
			<div class="panel panel-info" >
				 <div class="panel-heading"><strong>Select population of a country:</strong></div>
				 <div class="panel-body">
					<select bind:value={S0} >
						{#each countriesPopulation as countryPopulation}
							<option value={countryPopulation.population}>
								{countryPopulation.country}
							</option>
						{/each}
					</select>

					<div class="alert alert-info small"><strong>Information regarding the countries' population</strong> You can modify the susceptible parameter's value by selecting a specific country. These data were updated on 27/05/2020.</div> 

				</div>
			</div>
		</div>

	<div class="panel-group col-md-4 p-2 "  style="display:inline-block;">
	<div class="panel panel-info">
	 <div class="panel-heading"><strong>Set up which factors to be included in the model</strong></div>
	 <div class="panel-body">
<label><input type="checkbox" bind:group={parameterSelection} value={17} /> Humidity</label>
	<label style="visibility: hidden;"><input type="checkbox" bind:group={parameterSelection} value={18} /> Tempreture</label>
	

	{#if parameterSelection.length === 0}
	<p>Please select at least one parameter</p>
{/if}
	</div>
	</div>
	</div>

	

			<div class="panel-group col-md-7 p-1"  style="display:inline-block;">
	<div class="panel panel-info">
	 <div class="panel-heading"><strong>Set values of parameters</strong></div>
	 <div class="panel-body">

			<table class="table table-bordered">
					<thead>
					<tr>
						<th class="small">Definition:</th>
						<th class="small">Range of value:</th>
						<th class="small">Selected value:</th>
						<th class="small">Included in model:</th>
					</tr>
					</thead>
					<tbody>
						<tr>
							<SvelteTooltip tip="Initial contact rate" left color="#a0daa2"><td>$$c_0$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="12.48" max="45" step="0.3252" bind:value={c0} class="slider" id="myRange">
							</td>
							<td>{c0.toFixed(2)}</td>
							<td><label><input type="checkbox" bind:group={parameterSelection} value={0} /></label></td>
						</tr>

						<tr>
							<SvelteTooltip tip="Min. contact rate under control strategies" left color="#a0daa2"><td>$$c_a$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="1.0" max="6.0" step="0.05" bind:value={ca} class="slider" id="myRange">
							</td>
							<td>{ca}</td>
							<td><label><input type="checkbox" bind:group={parameterSelection} value={1} /></label></td>
						</tr>

						<tr>
							<SvelteTooltip tip="Initial quarantined rate under control strategies" left color="#a0daa2"><td>$$q_1$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.00000000000015" max="0.000012" step="0.00000000005" bind:value={q0} class="slider" id="myRange">
							</td>
							<td>{q0}</td>
							<td><label><input type="checkbox" bind:group={parameterSelection} value={2} /></label></td>
						</tr>

						<tr>
							<SvelteTooltip tip="Probability of transmission per contact" left color="#a0daa2"><td>$$\beta_0$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.00000000000015" max="0.000000012" step="0.00000000005" bind:value={beta0} class="slider" id="myRange">
							</td>
							<td>{beta0}</td>
							<td><label><input type="checkbox" bind:group={parameterSelection} value={3} /></label></td>
						</tr>

						<tr>
							<SvelteTooltip tip="Initial quarantined rate of exposed individuals" left color="#a0daa2"><td>$$\epsilon$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range"  min="0.000000000015" max="0.0012" step="0.000000005" bind:value={eps} class="slider" id="myRange">
							</td>
							<td>{eps}</td>
							<td><label><input type="checkbox" bind:group={parameterSelection} value={4} /></label></td>
						</tr>


						<tr>
							<SvelteTooltip tip="Transition rate of exposed individuals to the infected class" left color="#a0daa2"><td>$$\sigma$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.0666" max="0.333" step="0.02" bind:value={sigma} class="slider" id="myRange">
							</td>
							<td>{sigma}</td>
							<td><label><input type="checkbox" bind:group={parameterSelection} value={5} /></label></td>
						</tr>


						<tr>
							<SvelteTooltip tip="Rate at which the quarantined uninfects were released into the wider community" left color="#a0daa2"><td>$$\lambda$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.02" max="1" step="0.1" bind:value={lambda} class="slider" id="myRange">
							</td>
							<td>{lambda}</td>
							<td><label><input type="checkbox" bind:group={parameterSelection} value={6} /></label></td>
						</tr>

				
						<tr>
							<SvelteTooltip tip="Transition rate of symptomatic infected individuals to the quarantined infected class" left color="#a0daa2"><td>$$\delta_I$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.01" max="0.95" step="0.0094" bind:value={deltaI} class="slider" id="myRange">
							</td>
							<td>{deltaI}</td>
							<td><label><input type="checkbox" bind:group={parameterSelection} value={7} /></label></td>
						</tr>
						<tr>
							<SvelteTooltip tip="Transition rate of quarantined exposed individuals to the quarantined infected class" left color="#a0daa2"><td>$$\delta_q$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.01" max="0.4" step="0.0074" bind:value={deltaq} class="slider" id="myRange">
							</td>
							<td>{deltaq}</td>
							<td><label><input type="checkbox" bind:group={parameterSelection} value={8} /></label></td>
						</tr>

						<tr>
							<SvelteTooltip tip="Recovery rate of symptomatic infected individuals" left color="#a0daa2"><td>$$\gamma_I$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.01" max="0.75" step="0.0074" bind:value={gammaI} class="slider" id="myRange">
							</td>
							<td>{gammaI}</td>
							<td><label><input type="checkbox" bind:group={parameterSelection} value={9} /></label></td>
						</tr>

						<tr>
							<SvelteTooltip tip="Recovery rate of asymptomatic infected individuals" left color="#a0daa2"><td>$$\gamma_A$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.01" max="0.36" step="0.0035"  bind:value={gammaA} class="slider" id="myRange">
							</td>
							<td>{gammaA}</td>
							<td><label><input type="checkbox" bind:group={parameterSelection} value={10} /></label></td>
						</tr>

						<tr>
							<SvelteTooltip tip="Gamma h" left color="#a0daa2"><td>$$\gamma_H$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.01" max="0.1" step="0.001" bind:value={gammaH} class="slider" id="myRange">
							</td>
							<td>{gammaH}</td>
							<td><label><input type="checkbox" bind:group={parameterSelection} value={11} /></label></td>
						</tr>
						<tr>
						<SvelteTooltip tip="Rate at which recovered individuals move into pre-symptomatic class" left color="#a0daa2"><td>$$\gamma_R$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.0000003" max="0.00001" step="0.001" bind:value={gammaR} class="slider" id="myRange">
							</td>
							<td>{gammaR}</td>
							<td><label><input type="checkbox" bind:group={parameterSelection} value={12} /></label></td>
						</tr>
						<tr>
						<SvelteTooltip tip="Relative transmission probability of A compared with I" left color="#a0daa2"><td>$$\theta$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.0000003" max="0.1" step="0.0001" bind:value={theta} class="slider" id="myRange">
							</td>
							<td>{theta}</td>
							<td><label><input type="checkbox" bind:group={parameterSelection} value={13} /></label></td>
						</tr>

						<tr>
						<SvelteTooltip tip="Governmental action strength" left color="#a0daa2"><td>$$\alpha_b$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0" max="1" step="0.001" bind:value={alfab} class="slider" id="myRange">
							</td>
							<td>{alfab}</td>
							<td><label><input type="checkbox" bind:group={parameterSelection} value={14} /></label></td>
						</tr>
						<tr>
						<SvelteTooltip tip="Alfa" left color="#a0daa2"><td>$$\alpha$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0" max="1" step="0.001" bind:value={alfa} class="slider" id="myRange">
							</td>
							<td>{alfa}</td>
							<td><label><input type="checkbox" bind:group={parameterSelection} value={15} /></label></td>
						</tr>

						<tr>
						<SvelteTooltip tip="Maximum quarantined rate of exposed individuals" left color="#a0daa2"><td>$$c_2$$</td></SvelteTooltip>
							<td class="slidecontainer">
								<input type="range" min="0.00000000000015" max="0.000012" step="0.00000000005" bind:value={c2} class="slider" id="myRange">
							</td>
							<td>{c2}</td>
							<td><label><input type="checkbox" bind:group={parameterSelection} value={16} /></label></td>
						</tr>
						

					</tbody>
				</table>
	</div>
	</div>
	</div>


	<!-- <div class="panel-group  col-md-4 p-2 " style="display:inline-block;">
			<div class="panel panel-success" >
				 <div class="panel-heading"><strong>Select a template:</strong></div>
				 <div class="panel-body">
					<select bind:value={selectedCountry} on:click={changeInitialValue}>
						{#each countries as country}
							<option value={country}>
								{country.text}
							</option>
						{/each}
					</select>

					<div class="alert alert-info"><strong>Information regarding the templates!</strong> The parameters' values are calculated by IPSO algorithm. This is a guidance, it does not necessarily correspond to reality.</div> 

				</div>
			</div>
		</div> -->


	{#if parameterSelection.includes(18)}
	
			<div class="panel-group col-md-5 p-2 "  style="display:inline-block;">
	<div class="panel panel-info">
	 <div class="panel-heading"><strong>Set values of tempreture</strong></div>
	 <div class="panel-body">
		<Cubic temp={tempreture} on:myClick={myClickC}/>
	</div>
	</div>
	</div>
	{/if}
		
{#if parameterSelection.includes(17)}
		<div class="panel-group col-md-5 p-2 "  style="display:inline-block;">
	<div class="panel panel-info">
	 <div class="panel-heading"><strong>Set values of absolute humidity</strong></div>
	 <div class="panel-body">
				<CubicTau humidity={relativeHumidity} on:myClick={myClickTau}/>

	</div>
	</div>
	</div>

	
	
		
	{/if}

	<div class="panel-group col-md-8 p-2 "  style="display:inline-block;">
	<div class="panel panel-primary">
	 <div class="panel-heading"><strong>Data visualization </strong></div>
	 <div class="panel-body">
		<RRChart data={data} />


	</div>
	</div>
	</div>


			<div class="panel-group col-md-4 p-3 "  style="display:inline-block;">
	<div class="panel panel-info">
	 <div class="panel-heading"><strong>Parameter import/export</strong></div>
	 <div class="panel-body">
	 <div  class="alert alert-info" style="display:inline-block;" >Import JSON file:</div>
	<input class="btn btn-primary" bind:files type='file' >
	<div class="alert alert-info small"><strong>Information regarding importing files</strong> For importing data you need to upload a JSON file. You do not need to upload every parameter, just those one, which you want to modify. Other parameters will have the same value as they had previously. If you need a proper JSON structure, you can dowload the current parameter setup.</div> 
	<button class="btn btn-primary" on:click={handleClick}>
	Export
	</button>
</div>
	</div>
	</div>
	


			<div class="panel-group col-md-12 p-2 "  style="display:inline-block;">
	<div class="panel panel-success">
	 <div class="panel-heading"><strong>Information about model</strong></div>
	 <div class="panel-body" style="text-align:centre">
	
	 <div class="alert alert-warning col-md-6 p-2"  style="display:inline-block;">
	  <strong>Differential equation system:</strong>
	$$S'=-\left[\beta(t) c(t)+c(t)q(t)\left(1-\beta(t)\right)\right]S\left(I+\theta A\right)+\lambda S_q$$
			$$E'=\beta(t) c(t)\left(1-q(t)\right)S\left(I+\theta A\right)-\sigma E$$
			$$I'=\sigma\varrho E-\left(\delta_I+\alpha+\gamma_I\right)I$$
			$$A'=\sigma\left(1-\varrho\right)E-\gamma_A A+\gamma_R R$$
			$$S_q'=\left(1-\beta(t)\right)c(t)q(t)S\left(I+\theta A\right)-\lambda S_q$$
			$$E_q'=\beta(t) c(t)q(t)S\left(I+\theta A\right)-S_q E_q$$
			$$H'=\delta_I I+\delta_q E_q -\left(\alpha+\gamma_H \right)H$$
			$$R'=\gamma_I I+\gamma_A A+\gamma_H H-\gamma_R R$$
			</div>

			
			<div  class="alert alert-warning col-md-6 p-2"  style="display:inline-block;">
			<strong>Other functions in the system:</strong>
			$$c(t)=c_a+3(c_0-c_a)/(1+2b^-t)$$
			$$q(t)=(q_1t+q_0)/(t+1)$$
			<strong>In case of the presence of humidity and tempreture:</strong>
			$$\beta(t)=(1-\alpha_\beta)\beta_0\cdot(1+\xi AH(t))\cdot \left(1-(I(t)+A(t))/(S(t)+R(t))\right)^2$$
			<strong>In case of the presence of tempreture:</strong>
			$$\beta(t)=(1-\alpha_\beta)\beta_0\cdot(1+\xi AH(t))\cdot \left(1-(I(t)+A(t))/(S(t)+R(t))\right)^2.$$

			
			</div>
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
  width: 40%; /* Width of the outside container */
}

/* The slider itself */
.slider {
  -webkit-appearance: none;  /* Override default CSS styles */
  appearance: none;
  width: 100%; /* Full-width */
  height: 10px; /* Specified height */
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
  width: 13px; /* Set a specific slider handle width */
  height: 13px; /* Slider handle height */
  background: #4CAF50; /* Green background */
  cursor: pointer; /* Cursor on hover */
}

.slider::-moz-range-thumb {
  width: 13px; /* Set a specific slider handle width */
  height: 13px; /* Slider handle height */
  background: #a0daa2; /* Green background */
  cursor: pointer; /* Cursor on hover */
}
</style>
