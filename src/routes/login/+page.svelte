<script lang="ts">
	import { GoogleAuthProvider, signInWithPopup, signOut } from 'firebase/auth';
	import { auth,user } from '$lib/firebase';
  import {Button} from '$lib/components';

	async function signInWithGoogle() {
    const provider = new GoogleAuthProvider();
	//this will store a json web token on client (not on server)
	//for further uath checks
    const credential = await signInWithPopup(auth, provider);
	console.log(credential);

  //creating cookie on the server and setting it in the browser
  //for server side authentication
    const idToken = await credential.user.getIdToken();
    const res = await fetch("/api/signin", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        //csrf token for cross site request forgery attacks
        //but sveltekit does this automatically
        //so no need to set csrf token
        // 'CSRF-Token': csrfToken  // HANDLED by sveltekit automatically
      },
      body: JSON.stringify({ idToken }),
    });
  }

  async function signOutSSR() {
    //delete the server generated cookie when the user signs out
    const res = await fetch("/api/signin", { method: "DELETE" });
    await signOut(auth);
  }


</script>

<h2>Login</h2>

{#if $user}
  <h3>Welcome, {$user.displayName}</h3>
  <p class="text-success">You are logged in</p>
  <Button element="button" on:click={signOutSSR}
    >Sign out</Button
  >
{:else}
  <Button element="button" on:click={signInWithGoogle}
    >Sign in with Google</Button
  >
{/if}


