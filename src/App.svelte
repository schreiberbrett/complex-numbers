<script lang="ts">
	type Complex = {
		n: number
		k: number
	}

	type Normal = {
		a: number
		b: number
	}

	let current: Complex = {
		n: 0,
		k: 0
	}

	let stack: Complex[] = []

	function plus(x: Normal, y: Normal): Normal {
		return {a: x.a + y.a, b: x.b + y.b}
	}

	function normalize({n, k}: Complex): Normal {
		return {
			a: n * Math.cos(Math.PI * k / 2),
			b: n * Math.sin(Math.PI * k / 2)
		}
	}

	let kind: 'Stretch First' | 'Rotate First' | 'Simultaneous' = 'Rotate First'

	function gradient({n, k}: Complex): Complex[] {
		const numberOfPoints = 100

		const nStep = n / numberOfPoints
		const kStep = k / numberOfPoints

		let result: Complex[] = []

		switch (kind) {
			case 'Rotate First':
				for (let i = 0; i < numberOfPoints; i++) {
					result.push({n: 0, k: i * kStep})
				}

				for (let i = 0; i < numberOfPoints; i++) {
					result.push({n: i * nStep, k: k})
				}
				break

			case 'Stretch First':
				for (let i = 0; i < numberOfPoints; i++) {
					result.push({n: i * nStep, k: 0})
				}

				for (let i = 0; i < numberOfPoints; i++) {
					result.push({n: n, k: i * kStep})
				}
				break

			case 'Simultaneous':
				for (let i = 0; i < numberOfPoints; i++) {
					result.push({n: i * nStep, k: i * kStep})
				}

				break


		}

		return result
	}

	function fullGradient({a, b}: Normal, complex: Complex): Normal[] {
		return gradient(complex).map(({n, k}) => {
			const normal = normalize({n, k})
			return {a: a + normal.a, b: b + normal.b}
		})
	}

	function pathways(numbers: Complex[]): Normal[] {
		let result: Normal[] = [{a: 0, b: 0}]

		for (let i = 0; i < numbers.length; i++) {
			result.push(...fullGradient(result[result.length - 1], numbers[i]))
		}
	
		return result
	}

	let points: Normal[]
	$: points = pathways([...stack, current])

	let total: Normal
	$: total = points[points.length - 1]

	// ********************* graph *********************
	const DEFAULT_BOUND_MAGNITUDE = Math.ceil(Math.PI);
	const GRAPH_AXIS_MARK_LENGTH = 0.08;
	const xMaxBound = DEFAULT_BOUND_MAGNITUDE
	const xMinBound = -DEFAULT_BOUND_MAGNITUDE
	const yMaxBound = DEFAULT_BOUND_MAGNITUDE
	const yMinBound = -DEFAULT_BOUND_MAGNITUDE

</script>
<div class="outer">
<div class="container">
	<p>
		<button class={kind === 'Rotate First' ? 'highlighted' : ''}  on:click={_ => {
			stack = [...stack]
			kind = 'Rotate First'
		}}>Rotate then stretch</button>
		<button class={kind === 'Stretch First' ? 'highlighted' : ''}  on:click={_ => {
			stack = [...stack]
			kind = 'Stretch First'
		}}>Stretch then rotate</button>
		<button class={kind === 'Simultaneous' ? 'highlighted' : ''}  on:click={_ => {
			stack = [...stack]
			kind = 'Simultaneous'
		}}>Simultaneous</button>
	</p>

	<svg class="cartesian" viewBox="{xMinBound} {yMinBound} {(xMaxBound - xMinBound)} {(yMaxBound - yMinBound)}">
		<g>
			<!-- x and y axis -->
			<line stroke="black" fill="none" x1={xMinBound} y1="0" x2={xMaxBound} y2="0" />
			<line stroke="black" fill="none" x1="1"  y1={GRAPH_AXIS_MARK_LENGTH} x2="1"  y2={-GRAPH_AXIS_MARK_LENGTH} />
			<line stroke="black" fill="none" x1="2"  y1={GRAPH_AXIS_MARK_LENGTH} x2="2"  y2={-GRAPH_AXIS_MARK_LENGTH} />
			<line stroke="black" fill="none" x1="3"  y1={GRAPH_AXIS_MARK_LENGTH} x2="3"  y2={-GRAPH_AXIS_MARK_LENGTH} />
			<line stroke="black" fill="none" x1="4"  y1={GRAPH_AXIS_MARK_LENGTH} x2="4"  y2={-GRAPH_AXIS_MARK_LENGTH} />
			<line stroke="black" fill="none" x1="-1" y1={GRAPH_AXIS_MARK_LENGTH} x2="-1" y2={-GRAPH_AXIS_MARK_LENGTH} />
			<line stroke="black" fill="none" x1="-2" y1={GRAPH_AXIS_MARK_LENGTH} x2="-2" y2={-GRAPH_AXIS_MARK_LENGTH} />
			<line stroke="black" fill="none" x1="-3" y1={GRAPH_AXIS_MARK_LENGTH} x2="-3" y2={-GRAPH_AXIS_MARK_LENGTH} />
			<line stroke="black" fill="none" x1="-4" y1={GRAPH_AXIS_MARK_LENGTH} x2="-4" y2={-GRAPH_AXIS_MARK_LENGTH} />
			<line stroke="black" fill="none" x1="0" y1={yMinBound} x2="0" y2={yMaxBound} />
			<line stroke="black" fill="none" x1={-GRAPH_AXIS_MARK_LENGTH} y1="1"  x2={GRAPH_AXIS_MARK_LENGTH} y2="1" />
			<line stroke="black" fill="none" x1={-GRAPH_AXIS_MARK_LENGTH} y1="2"  x2={GRAPH_AXIS_MARK_LENGTH} y2="2" />
			<line stroke="black" fill="none" x1={-GRAPH_AXIS_MARK_LENGTH} y1="3"  x2={GRAPH_AXIS_MARK_LENGTH} y2="3" />
			<line stroke="black" fill="none" x1={-GRAPH_AXIS_MARK_LENGTH} y1="4"  x2={GRAPH_AXIS_MARK_LENGTH} y2="4" />
			<line stroke="black" fill="none" x1={-GRAPH_AXIS_MARK_LENGTH} y1="-1" x2={GRAPH_AXIS_MARK_LENGTH} y2="-1" />
			<line stroke="black" fill="none" x1={-GRAPH_AXIS_MARK_LENGTH} y1="-2" x2={GRAPH_AXIS_MARK_LENGTH} y2="-2" />
			<line stroke="black" fill="none" x1={-GRAPH_AXIS_MARK_LENGTH} y1="-3" x2={GRAPH_AXIS_MARK_LENGTH} y2="-3" />
			<line stroke="black" fill="none" x1={-GRAPH_AXIS_MARK_LENGTH} y1="-4" x2={GRAPH_AXIS_MARK_LENGTH} y2="-4" />
			
			<!-- graph of function -->
			<polyline stroke="black" stroke-width="40" fill="none" points={points.map(({a, b}) => `${a},${b}`).join(' ')} />

			<circle fill="black" r=".05" cx={total.a} cy={total.b}></circle>
		</g>
	</svg>

	{#each stack as complex}
		{complex.n} * i ^ {complex.k} +
	{/each}

	{current.n} * i ^ {current.k} = {total.a.toFixed(2)} + {total.b.toFixed(2)}i

	<label for="n">n</label>
	<input id="n" type="range" min="0" max="10" step=".1" bind:value={current.n}>
	<label for="k">k</label>
	<input id="k" type="range" min="0" max="4" step=".1" bind:value={current.k}>

	<button on:click={_ => {
		stack = [...stack, current]
		current = {n: 0, k: 0}
	}}>Add</button>
</div>
</div>
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.12.0/dist/katex.min.css" integrity="sha384-AfEj0r4/OFrOo5t7NnNe46zW/tFgW6x/bCJG8FqQCEo3+Aro6EYUG4+cU+KJWu/X" crossorigin="anonymous">
<style>
	.outer {
		width: 100%;
	}
	.riemann-rectangle {
		fill: gray;
		stroke: black;
		stroke-width: 1;
	}
	line, rect, polyline {
		stroke-width: 1px;
		vector-effect: non-scaling-stroke;
	}
	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
	input[type="range"], svg.cartesian {
		width: min(65vh, 100%);
		display: block;
		margin: 0 auto;
	}
	.container {
		width: min(70vh, 100%);
		display: block;
		margin: 0 auto;
	}
	/* Flip the vertical axis in <g> to emulate cartesian. */
	svg.cartesian > g {
		transform: scaleY(-1);
	}
	/* Re-flip all <text> element descendants to their original side up. */
	svg.cartesian > g text {
		transform: scaleY(-1);
	}
	.highlighted {
		background-color: limegreen;
	}
	label {
		width: max-content;
		font-size: 20px;
	}
	#SecantVsTangent {
		font-size: 30px;
	}
</style>