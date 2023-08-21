<script lang="ts">
	import { page } from "$app/stores";
	import AnimatedRoute from "$lib/components/AnimatedRoute.svelte";
	</script>
	
	
	<!-- stepper using daisyui -->
	<nav class="stepper-nav">
		<ul class="steps">
		  <a href="/login" class="step step-primary">Sign In</a>
		  <a
			href="/login/username"
			class="step"
			class:step-primary={$page.route.id?.match(/username|photo/g)}>
			Choose Username
		  </a>
		  <a
			href="/login/photo"
			class="step"
			class:step-primary={$page.route.id?.includes("photo")}>
			Upload Photo
		  </a>
		</ul>
	  </nav>
	
	  <AnimatedRoute>
	<main class="card main-wrapper">
		<div class="content">
		  <slot />
		</div>
	</main>
	</AnimatedRoute>

	<style lang='scss'>
		.stepper-nav{
			display: flex;
			justify-content: center;
			margin: functions.toRem(24) 0;
		}
		.main-wrapper{
			width: 66.6667%;
			margin: 0 auto;
		}


		.steps a{
			color: hsl(var(--p)/1);
		}

	</style>
