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
			color: {
				bg: '#0D192B',
				fg: [ '#07F9A2', '#09C184', '#0A8967' ]
			},
			gradient: null,
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

			// fill background
			this.s.rect({
				x: 0,
				y: 0,
				width: this.w,
				height: this.h,
				fill: this.color.bg
			});

			// set up gradients
			// var rgb = Snap.getRGB(this.color.fg[0]);
			// console.log(rgb);
			// var rgba ='rgba(' + rgb.r + ',' + rgb.g + ',' + rgb.b + ',0)';
			// console.log(rgba);
			this.gradient = this.s.gradient('r(0.5, 0.5, 0.5)' + this.hexToRGBA(this.color.fg[0], 1) + '-' + this.hexToRGBA(this.color.fg[0], 0) );
			console.log(this.gradient.stops()[0]);
		},
		draw() {
			for (var num = 0; num < 5; num++){
				var fill = this.varyColor(this.color.fg[0]);
				var g = 'r()' + this.hexToRGBA(fill, 1) + '-' + this.hexToRGBA(fill, 0);
				var newShape = this.s.circle({
					cx: this.w/2,
					cy: this.h/2,
					r: Math.min(this.w, this.h)/10,
					// fillOpacity: 0.7,
					// fill: this.color.fg[0]
					fill: g
				}).click(this.click);
				newShape.attr({ id: newShape.id });
			}
		},
		hexToRGBA(hex,alpha){
			var rgb = Snap.getRGB(hex);
			var rgba ='rgba(' + rgb.r + ',' + rgb.g + ',' + rgb.b + ',' + alpha + ')';
			return rgba;
		},
		varyColor(input){
			var color = Snap.getRGB(input);
			color.r += chance.normal({dev: 20});
			color.g += chance.normal({dev: 20});
			color.b += chance.normal({dev: 20});
			return Snap.rgb(color.r, color.g, color.b);
		},
		click(event) {
			// var newCol = chance.color();
			// var g = 'r()' + this.hexToRGBA(newCol, 1) + '-' + this.hexToRGBA(newCol, 0);

			var fill = this.varyColor(this.color.fg[0]);
			var g = 'r()' + this.hexToRGBA(fill, 1) + '-' + this.hexToRGBA(fill, 0);

			// console.log(event.target.id);
			this.s.select('#' + event.target.id).attr(
				{
					fill: g
				},
				// 1000
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
