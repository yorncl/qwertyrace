<template>
	<div>
		<style>
			:root {
			--len: {{ Math.min(points, 100) }}%;
			}
		</style>
		<div v-bind:class="{shaking: points > 100}" class="lifebar">
			<div></div>
		</div>
	</div>
</template>
<script>
export default {
	props : {
		points : Number
	},
	data()
	{
		return {

		}
	}
}
</script>
<style >

div.lifebar {
	background-color: rgba(0, 0, 0, 0.3);
	height: 40px;
	width: 30vw;
}

div.lifebar > div {
	transition: all 0.25s;
	height: 100%;
	width: var(--len);
	background-color: white;
}

div.shaking {
	animation-name: shake;
	animation-duration: 0.5s;
	animation-iteration-count: infinite;
	animation-direction: alternate;
}

@keyframes shake {
  0%, 100% {
    transform: translate3d(0, 0, 0);
  }
  
  10%, 90% {
    transform: translate3d(-1px, 0, 0);
  }
  
  20%, 80% {
    transform: translate3d(2px, 0, 0);
  }

  30%, 50%, 70% {
    transform: translate3d(-4px, 0, 0);
  }

  40%, 60% {
    transform: translate3d(4px, 0, 0);
  }
}

</style>