<script lang='ts'>
    import { page } from "$app/stores";
    export let title: string;
    export let description: string;
    export let type: string;
    $: url = $page.url.href;
  </script>
  
<!-- everything in svelte:head goes to head of app.html -->
  <svelte:head>
    <title>{title}</title>
    <meta name="description" content={description} />
    {@html `<script type="application/ld+json">
  {
      "@context": "http://schema.org",
      "@type": "${type}",
        "name": "${title}",
        "description": "${description}",
        "url": "${url}",
      }
    </script>`}
  </svelte:head>