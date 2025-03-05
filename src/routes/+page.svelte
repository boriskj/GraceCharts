<script lang="ts">
	import { Line } from 'svelte-chartjs';
	import { Pie } from 'svelte-chartjs';
	import { data } from '$lib/data.js';

	import {
		Chart as ChartJS,
		Title,
		Tooltip,
		Legend,
		LineElement,
		LinearScale,
		PointElement,
		ArcElement,
		CategoryScale
	} from 'chart.js';
	import { each } from 'chart.js/helpers';

	ChartJS.register(
		Title,
		Tooltip,
		Legend,
		LineElement,
		LinearScale,
		ArcElement,
		PointElement,
		CategoryScale
	);
	type ChartData = {
		labels: (string | number)[];
		datasets: {
			label: string;
			fill?: boolean;
			borderColor?: string;
			data: number[];
			backgroundColor?: string[];
			hoverBackgroundColor?: string[];
		}[];
	};

	let year: number = 2008;

	let chart1: ChartData = {
		labels: data.map((el) => el.year),
		datasets: [
			{
				label: 'female',
				fill: false,
				borderColor: 'rgb(255, 99, 132)',
				data: data.map((y) => y.f)
			},
			{
				label: 'male',
				fill: false,
				borderColor: 'rgb(54, 162, 235)',
				data: data.map((y) => y.m)
			}
		]
	};

	let chart2: ChartData = {
		labels: ['male', 'female'],
		datasets: [
			{
				label: 'female',
				fill: false,
				backgroundColor: ['rgb(54, 162, 235)', 'rgb(255, 99, 132)'],
				data: getDataByYear(year) ?? [0, 0]
			}
		]
	};

	$: {
		chart2.datasets[0].data = getDataByYear(year) ?? [0, 0];
	}

	function getDataByYear(year: number) {
		const output = data.find((el) => el.year == year);

		if (output) return [output?.m, output.f];
		else return null;
	}

	function maxDiff() {
		let max = [undefined, 0];
		data.forEach((el) => {
			let diff = Math.abs(el.f - el.m);
			if (diff > max[1]) {
				max = [el.year, diff];
			}
		});
		return max;
	}

	function maxDiffPerc() {
		let max = [undefined, 0];
		data.forEach((el) => {
			let total = el.f + el.m;
			let diff = Math.abs((el.f / total - el.m / total) * 100).toFixed(3);
			if (diff > max[1]) max;
			max = [el.year, diff];
		});

		return max;
	}

	console.log(getDataByYear(year));
	console.log(maxDiff());
</script>

<div class="container mx-auto">
	<div class="grid grid-cols-2 gap-10 mb-10">
		<div class="border-4 border-black p-3">
			<h2 class="text-center text-2xl text-black font-bold">data grah🫛</h2>
			<Line data={chart1} options={{ responsive: true }} />
		</div>
		<div class="border-4 border-black p-3">
			<h2 class="text-center text-2xl text-black font-bold">data grah 🫛</h2>
			<input
				bind:value={year}
				class="border-black border-2 text-center"
				type="number"
				min="1954"
				max="2023"
			/>
			<Pie data={chart2} options={{ responsive: true }} />
		</div>
	</div>
	<div class="grid grid-cols-2 gap-10 mb-10">
		<div class="border-4 border-black p-3">
			<h2 class="text-center text-2xl text-black font-bold">največja razlika🫛</h2>
			<p>Leta {maxDiff()[0]} je bila razlika {maxDiff()[1]}</p>
		</div>
	</div>
	<div class="grid grid-cols-2 gap-10 mb-10">
		<div class="border-4 border-black p-3">
			<h2 class="text-center text-2xl text-black font-bold">največja razlika v %🫛</h2>
			<p>Leta {maxDiffPerc()[0]} je bila razlika {maxDiffPerc()[1]} %</p>
		</div>
	</div>
</div>
