<script lang="ts">
    import AuthCheck from "$lib/components/AuthCheck.svelte";
    import { db, user, userData } from "$lib/firebase";
    import { doc, getDoc, writeBatch } from "firebase/firestore";
    import {Button} from '$components';
    let username = "";
    let loading = false;
    let isAvailable = false;

    //run check availability function only after 500ms when user
    //starts typing
    let debounceTimer: NodeJS.Timeout;
   
    const re = /^(?=[a-zA-Z0-9._]{3,16}$)(?!.*[_.]{2})[^_.].*[^_.]$/;
    
    $: isValid =
      username?.length > 2 && username.length < 16 && re.test(username);
    $: isTouched = username.length > 0;
    $: isTaken = isValid && !isAvailable && !loading;
    
    // TODO ADD FIREBASE SECURITY RULES
    function checkAvailability() {
      isAvailable = false;
      clearTimeout(debounceTimer);
      if (!isValid) {
        loading = false;
        return;
      }
      loading = true;
      debounceTimer = setTimeout(async () => {
        console.log("checking availability of", username);


        //usernames collection has username as doc id and uid as data
        //users collection has uid as doc id and username as data
        const ref = doc(db, "usernames", username);
        const exists = await getDoc(ref).then((doc) => doc.exists());
        isAvailable = !exists;
        loading = false;
      }, 500);
    }


    async function confirmUsername() {
      console.log("confirming username", username);
      const batch = writeBatch(db);
      batch.set(doc(db, "usernames", username), { uid: $user?.uid });
      batch.set(doc(db, "users", $user!.uid), {
        username,
        photoURL: $user?.photoURL ?? null,
        published: true,
        bio: "I am the King",
        links: [
          {
            title: "Test Link",
            url: "https://kung.foo",
            icon: "custom",
          },
        ],
      });
      await batch.commit();
      username = "";
      isAvailable = false;
    }
  </script>
  
  <AuthCheck>
    {#if $userData?.username}
      <h3>
        Your username is <span class="text-success font-bold"
          >@{$userData.username}</span
        >
      </h3>
      <h6 class='text-warning'>(Usernames cannot be changed)</h6>
      <Button element='a' href="/login/photo" variant='outline'>Upload Profile Image</Button>
    {:else}
      <form class="username-form" on:submit|preventDefault={confirmUsername}>
        <input
          type="text"
          placeholder="Username"
          class="input"
          class:error={!isValid && isTouched && !isAvailable}
          bind:value={username}
          on:input={checkAvailability}
          class:input-error={!isValid && isTouched}
          class:input-warning={isTaken}
          class:input-success={isAvailable && isValid && !loading}
        />
        <div class="error-box">
          {#if loading}
            <p>Checking availability of @{username}...</p>
          {/if}
  
          {#if !isValid && isTouched}
            <p class="text-error">
              must be 3-16 characters long, alphanumeric only
            </p>
          {/if}
  
          {#if isValid && !isAvailable && !loading}
            <p class="text-warning">
              @{username} is not available
            </p>
          {/if}
  
          {#if isAvailable}
            <Button element="button" type='submit' variant='outline'>Confirm username @{username} </Button>
          {/if}
        </div>
      </form>
    {/if}
  </AuthCheck>


  <style lang='scss'>
    .username-form{
       width: 80%;
       input{
          width: 100%;
       }
       .error-box{
        margin: 1rem 0;
        min-height: 4rem;
        padding: 0 2rem;
        width: 100%;
        text-align: center;
       }
    }

  </style>