<script lang="ts">
  //sets standard css like padding etc on all pages
  import 'modern-normalize/modern-normalize.css';
    //global styles
    import '../styles/main.scss';
    import {page} from '$app/stores';

    import {Navigation} from '$components';


// for changing opacity of header on scroll
  let topbar: HTMLElement;
	let scrollY: number;
	let headerOpacity = 0;

  	// opacity will be 1 when we have scrolled equal to the height of topbar
	$: if (topbar) {
		headerOpacity = scrollY / topbar.offsetHeight < 1 ? scrollY / topbar.offsetHeight : 1;
	}
</script>


<svelte:window bind:scrollY />
<svelte:head>
	<title>Crowd-Stories {$page.data.title ? ` - ${$page.data.title}` : ''}</title>
</svelte:head>

<div id="main">
  <div id="sidebar">
    <Navigation desktop={true}/>
  </div>
  <div id="content">
    <div id="topbar" bind:this={topbar}>
      <div
      class="topbar-bg"
      style:background-color={$page.data.color ? $page.data.color: "var(--header-color)"}
      style:opacity={`${headerOpacity}`}
    />
topbar
    </div>
    <main id="main-content">
main content
<slot />
    </main>


</div>

<style lang='scss'>
  	#main {
		display: flex;

		
		#content {
			flex: 1;

			#topbar {
				position: fixed;
				height: var(--header-height);
				padding: 0 15px;
				display: flex;
				align-items: center;
				width: 100%;
				z-index: 100;

				.topbar-bg {
					position: absolute;
					width: 100%;
					height: 100%;
					top: 0;
					left: 0;
					z-index: -1;// need to make sure background is below content
					// if the color from album is too light we want the background to be dark
					// so that links appear
					// 0.2 is opacity
					//0,0,0 is black color
					background-image: linear-gradient(rgba(0,0,0,0.2) 0 0);
				}
				@include breakpoint.up('md') {
					padding: 0 30px;
					width: calc(100% - var(--sidebar-width));
				}
			}

			main#main-content {
				padding: 30px 15px 60px;

				@include breakpoint.up('md') {
					padding: 30px 30px 60px;
				}

			}
		}
	}
</style>