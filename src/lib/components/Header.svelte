<script lang='ts'>
	import { browser } from '$app/environment';
import { Navigation} from '$components';
// import { page } from '$app/stores';
import { ChevronDown, Sun, Moon  } from 'lucide-svelte';
import {tippy} from '$actions';  
import { userData,auth,user  } from "$lib/firebase";
import {Button} from '$components';
import { signOut } from 'firebase/auth';
import { onMount } from 'svelte';

let currentTheme = '';

onMount(() => {
    // currentTheme = document.documentElement.dataset.theme;

    const userPrefersDarkMode = window.matchMedia(
      '(prefers-color-scheme: dark)'
    ).matches;

    const hasUserSetDarkModeManually =
      document.documentElement.dataset.theme == 'dark';
      
    if (!hasUserSetDarkModeManually) {
      setTheme(userPrefersDarkMode ? 'dark' : 'light');
    }
    
  });

//export let userAllPlaylists: SpotifyApi.PlaylistObjectSimplified[] | undefined; 

//$: user = $page.data.user;
async function signOutSSR() {
    //delete the server generated cookie when the user signs out
    //const res = await fetch("/api/signin", { method: "DELETE" });
    await signOut(auth);
  }

  const setTheme = (theme: string) => {
    //document.documentElement points to html tag in app.html file
    //dataset.theme equals data-theme attribute
    document.documentElement.dataset.theme = theme;
    //31536000s is equal to one year
    document.cookie = `siteTheme=${theme};max-age=31536000;path="/"`;
    currentTheme = theme;
  };
</script>

<div class="content">
	<div class="left">
		<!-- on server both mobile and desktop sidebar menus will be rendered 
		so we want to render mobile sidebar menu only on the browser 
		and not on the server -->
		{#if browser}
		<Navigation desktop={false}/>
		{/if}
	</div>
	<div class="right">
		<div id="profile-button">
			<button class="profile-button"
			use:tippy={{
				content: document.getElementById('profile-menu') || undefined,
				onMount: () => {
					const template = document.getElementById('profile-menu');
					if (template) {
						template.style.display = 'block';
					}
				},
				trigger: 'click',
				placement: 'bottom-end',
				interactive: true,
				theme:'menu' // custom tippy theme in tippy-theme.scss file in styles folder
			}}
			>
				{#if $userData?.photoURL}
					<img src={$userData?.photoURL} alt="" />
				{/if}
				{$userData?.username ?? 'Guest'} <span class="visually-hidden">Profile menu</span>
				<ChevronDown class="profile-arrow" size={22} />
			</button>
		</div>
		<div class='theme'>
			{#if currentTheme == 'light'}
			<a href={'#'} on:click={() => setTheme('dark')}>
			<Moon aria-hidden="true" focusable="false" color="var(--text-color)" />
		     </a>
			{:else}
			<a  href={'#'} on:click={() => setTheme('light')}>
				<Sun aria-hidden="true" focusable="false" color="var(--text-color)" />
			</a>
			{/if}
		</div>

		<!-- dropdown menu  -->
		<div id="profile-menu" style="display: none;">
			<div class="profile-menu-content">
				<ul>
					
					{#if $user}
					<li><a href="/profile">View Profile</a></li>
					<li><Button element='button'  on:click={signOutSSR}>Logout</Button></li> 
					{:else}
					<li><a href="/login">Login</a></li>
					{/if}
				</ul>
			</div>
		</div>
	</div>
</div>

<style lang="scss">

	
	.content {
		display: flex;
		justify-content: space-between;
		align-items: center;
		width: 100%;

	}
	.right{
		display: flex;
		align-items: center;
		.theme{
			margin-left: 5px;
		}
	}
	.profile-button {
		background: none;
		border: 1px solid var(--border);
		padding: 5px;
		border-radius: 25px;
		display: flex;
		align-items: center;
		color: var(--text-color);
		cursor: pointer;

		:global(.profile-arrow) {
			margin-left: 3px;
		}
		img {
			width: 28px;
			height: 28px;
			border-radius: 100%;
			margin-right: 10px;
		}
		&:hover {
			background-color: var(--accent-color);
		}
	}
	.profile-menu-content {
		padding: 5px 0;
		ul {
			padding: 0;
			margin: 0;
			list-style: none;
			li {
				&:hover {
					//transparent white color linear gradient
					background-image: linear-gradient(rgba(255, 255, 255, 0.07) 0 0);
				}
				a :global(svg) {
					vertical-align: middle;
					margin-left: 10px;
				}
				a,
				// overriding styles of Button component
				:global(button) {
					display: inline-block;
					padding: 10px 15px;
					background: none; // removing background of button
					border: none;// removing border of button
					text-decoration: none;// removing text-decoration of links
					cursor: pointer;
					color: var(--menu-text-color);
					width: 100%;
					text-align: left; // for button
					font-size: functions.toRem(14);
					border-radius: 0;
					font-weight: 400;
					&:hover{
						background-image: none;
					}
				}
			}
		}
	}

</style>