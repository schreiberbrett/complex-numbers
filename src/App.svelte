<script lang="ts">
	import * as katex from 'katex'
	import { onMount, tick } from 'svelte/internal'

	onMount(() => {
		katex.render('n * (-1)^k', document.getElementById('formula'), {output: 'html'})
		renderAll()
	})

	let mode: 'Beginner' | 'Advanced' = 'Beginner'

	const colors: string[] = [
		'pink',
		'lightblue',
		'lightgreen',
		'orange',
		'lightgrey',
		'aquamarine',
		'chartreuse',
		'gold',
		'silver',
		'tomato',
		'yellowgreen'
	]

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

	function normalize({n, k}: Complex): Normal {
		return {
			a: n * Math.cos(Math.PI * k / 2),
			b: n * Math.sin(Math.PI * k / 2)
		}
	}


	function fullGradient({a, b}: Normal, {n, k}: Complex): Normal {
		const normal = normalize({n, k})
		return {a: a + normal.a, b: b + normal.b}
	}

	function pathways(numbers: Complex[]): Normal[] {
		let result: Normal[] = [{a: 0, b: 0}]

		for (let i = 0; i < numbers.length; i++) {
			result.push(fullGradient(result[result.length - 1], numbers[i]))
		}
	
		return result
	}

	let points: Normal[]
	$: points = pathways([...stack, current])

	let total: Normal
	$: total = points[points.length - 1]


	function pairwise<T>(xs: T[]): [T, T][] {
		let result: [T, T][] = []
		for (let i = 0; i < xs.length - 1; i++) {
			result.push([xs[i], xs[i + 1]])
		}

		return result
	}

	function renderAll() {
		for (let i = 0; i < stack.length; i++) {
			let {n, k} = stack[i]
			katex.render(` + ${n.toFixed(2)} * (-1)^{${(k / 2).toFixed(2)}}`, document.getElementById(`equation-${i}`), {output: 'html'})
		}

		katex.render(`${current.n.toFixed(2)} * (-1)^{${(current.k / 2).toFixed(2)}}`, document.getElementById('current'), {output: 'html'})
		katex.render(`= ${total.a.toFixed(2)}${(total.b < 0.01 && total.b > -0.01) ? '' : ` + ${total.b.toFixed(2)}i`}`, document.getElementById('total'), {output: 'html'})
	}

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
	<p>Adding up numbers of the form <span id="formula"></span></p>

	<svg class="cartesian" viewBox="{xMinBound} {yMinBound} {(xMaxBound - xMinBound)} {(yMaxBound - yMinBound)}">
		<g>
			{#each pairwise(points) as [fst, snd], i}
				<line stroke={colors[i % colors.length]} stroke-width="4" x1={fst.a} y1={fst.b} x2={snd.a} y2={snd.b}></line>
			{/each}

			<circle fill="black" r=".05" cx={total.a} cy={total.b}></circle>


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
		</g>
	</svg>


	<input type="range" min="0" max="8" step=".01" bind:value={current.n} on:input={renderAll}>
	<input type="range" min="0" max="4" step={mode === 'Beginner' ? 2 : 0.001} bind:value={current.k} on:input={renderAll}>

	<button id="add" on:click={_ => {
		stack = [...stack, current]
		current = {n: 0, k: 0}
		tick().then(renderAll)
	}}>Add</button>

	<p>
		<button id="beginner" class={mode === 'Beginner' ? 'highlighted' : ''}  on:click={_ => {
			stack = [...stack]
			mode = 'Beginner'
			renderAll()
		}}>Beginner</button>
		<button id="advanced" class={mode === 'Advanced' ? 'highlighted' : ''}  on:click={_ => {
			stack = [...stack]
			mode = 'Advanced'
			renderAll()
		}}>Advanced</button>
	</p>

	<div id="current" style={`background-color: ${colors[stack.length % colors.length]}`}>{current.n} * (-1) ^ {current.k / 2}</div>
	{#each stack as _, i}
	<div id={`equation-${stack.length - i - 1}`} style={`background-color: ${colors[stack.length - i - 1 % colors.length]}`}></div>
	{/each}
	<div id="total"></div>
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

	#add {
		width: 100%;
	}

	#beginner, #advanced {
		width: 49%;
	}

	#SecantVsTangent {
		font-size: 30px;
	}

	li {
		list-style: none;
	}
</style>