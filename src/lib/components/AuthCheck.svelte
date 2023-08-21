<script lang="ts">
    import { user } from "$lib/firebase";
    import {Button} from '$components';
  </script>
  
  {#if $user}
    <slot />
  {:else}
      <p class="text-error message">
          You must be signed in to view this page.
          <Button element="a" href="/login">Login</Button>
      </p>
  {/if}

  <style lang='scss'>
    .message{
      margin: functions.toRem(40) 0;
      text-align: center;
      :global(a) {
        margin-left: 10px;
      }
    }

  </style>