<script lang="ts">
    import AuthCheck from "$lib/components/AuthCheck.svelte";
    import { user, userData, storage, db } from "$lib/firebase";
    import { doc, updateDoc } from "firebase/firestore";
    import { getDownloadURL, ref, uploadBytes } from "firebase/storage";
    import {Button} from '$components';

    let previewURL: string;
    let uploading = false;
   
    $: href = `/${$userData?.username}/edit`;
   
    async function upload(e: any) {
      uploading = true;
      const file = e.target.files[0];
      //global URL object built in the browser
      previewURL = URL.createObjectURL(file);
      const storageRef = ref(storage, `users/${$user!.uid}/profile.png`);
      const result = await uploadBytes(storageRef, file);
      const url = await getDownloadURL(result.ref);
      await updateDoc(doc(db, "users", $user!.uid), { photoURL: url });
      uploading = false;
    }
  </script>
  
  <AuthCheck>
    <h2>Upload a Profile Photo</h2>
  
    <form class="photo-form">
      <div class="form-control content w-full max-w-xs my-10 mx-auto text-center">
        <img
          src={previewURL ?? $userData?.photoURL ?? "/user.png"}
          alt="photoURL"
          width="256"
          height="256"
        />
        <label for="photoURL" class="label">
          <span>Pick a file</span>
        </label>
        <input
          on:change={upload}
          name="photoURL"
          type="file"
          class="file-input file-input-bordered w-full max-w-xs"
          accept="image/png, image/jpeg, image/gif, image/webp"
        />
        {#if uploading}
          <p>Uploading...</p>
          <progress class="progress progress-info w-56 mt-6" />
        {/if}
      </div>
    </form>
  
    <Button {href} element='a'> Finish </Button>
  </AuthCheck>


  <style lang='scss'>
.photo-form{
  max-width: functions.toRem(768);
  width: 100%;
  .content{
      width: 100%;
      max-width: functions.toRem(320);
      margin: functions.toRem(40) auto;
      text-align: center;
  }
}
  </style>
