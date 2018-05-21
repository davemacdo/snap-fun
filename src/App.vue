<template>
	<div id="app">
		<svg id="snap"></svg>
	</div>
</template>

<script>
import Snap from 'snapsvg-cjs';
const Chance = require('chance');
const chance = new Chance();

// const Snap = require('snapsvg');

export default {
	name: 'app',
	data () {
		return {
			msg: 'I\'m sure you\'ll put something interesting here eventually.',
			s: null,
			shapes: {},
			interval: 1000,
			count: 0,
			intervalId: null,
			stepSize: 50,
			w: 0,
			h: 0,
			frame: 0
		}
	},
	methods: {
		setup() {
			this.s = Snap('#snap');
			// this.anim = setInterval(function () {console.log('log') }, this.interval);
		},
		draw() {
			for (var num = 0; num < 5; num++){
				var newShape = this.s.circle({
					cx: this.w/2,
					cy: this.h/2,
					r: Math.min(this.w, this.h)/10,
					fillOpacity: 0.5,
					fill: chance.color()
				}).click(this.click);
				newShape.attr({ id: newShape.id });
			}
		},
		click(event) {
			// console.log(event.target.id);
			this.s.select('#' + event.target.id).animate(
				{
					fill: chance.color()
				},
				1000
			);
		},
		move(coords, stepSize) {
			coords.cx = coords.cx + chance.normal({dev:stepSize});
			coords.cy = coords.cy + chance.normal({dev:stepSize});

			return coords;
		},
		go() {
			var toMove = this.s.selectAll('circle');

			for (var i = 0; i < toMove.length; i++) {
				var currentCoords = {
					cx: parseFloat(toMove[i].attr('cx')),
					cy: parseFloat(toMove[i].attr('cy'))
				};

				toMove[i].animate(
					this.move(currentCoords, this.stepSize),
					this.interval
				)
			};

			if (this.frame%50 == 0 && this.frame != 0){
				this.draw();
			}

			this.frame++;
			// this.s.selectAll('circle').animate(
			// 	this.move(currentCoords, 10),
			// 	this.interval
			// );
		}
	},
	mounted () {
		this.w = Math.max(document.documentElement.clientWidth, window.innerWidth || 0);
		this.h = Math.max(document.documentElement.clientHeight, window.innerHeight || 0);
		this.setup();
		this.draw();
		this.intervalId = setInterval(this.go, this.interval);
	}
}
</script>

<style lang="scss">
	@import './scss/style.scss'
</style>
