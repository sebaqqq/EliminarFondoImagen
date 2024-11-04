<script>
  import { onMount } from 'svelte';
  let image = null;
  let resultImage = null;
  let loading = false;

  function handleImageUpload(event) {
    image = event.target.files[0];
  }

  async function removeBackground() {
    if (!image) return;

    const formData = new FormData();
    formData.append('image_file', image);
    formData.append('size', 'auto');

    loading = true;

    try {
      const response = await fetch('https://api.remove.bg/v1.0/removebg', {
        method: 'POST',
        headers: {
          'X-Api-Key': 'QGeS3kUFoGPd8RHEhWyrbyRv', 
        },
        body: formData,
      });

      if (!response.ok) {
        throw new Error('Error al quitar el fondo');
      }

      const blob = await response.blob();
      resultImage = URL.createObjectURL(blob);
    } catch (error) {
      console.error('Error al quitar el fondo:', error);
    } finally {
      loading = false;
    }
  }
</script>

<style>
  button:disabled {
    cursor: not-allowed;
    opacity: 0.5;
  }
</style>

<h1>Quitar Fondo de Imagen</h1>
<input type="file" accept="image/*" on:change={handleImageUpload} />
<button on:click={removeBackground} disabled={!image || loading}>
  {#if loading}
    Procesando...
  {:else}
    Quitar Fondo
  {/if}
</button>

{#if resultImage}
  <div>
    <h2>Resultado:</h2>
    <img src={resultImage} alt="Resultado sin fondo" />
  </div>
{/if}
