<script>
  let primary = "#8199A3";
  let secondary = "#eee";

  let src;
  let canvas;
  let image;

  $: document.body.style.background = primary;

  function imageLoaded() {
    const ctx = canvas.getContext("2d");
    ctx.drawImage(image, 0, 0, canvas.width, canvas.height);
    let imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);

    const length = imageData.data.length;

    let count = 0;
    let rgb = { r: 0, g: 0, b: 0 };
    for (let i = 0; i < length; i += 4 * 5) {
      ++count;
      rgb.r += imageData.data[i];
      rgb.g += imageData.data[i + 1];
      rgb.b += imageData.data[i + 2];
    }

    Object.keys(rgb).forEach(key => {
      rgb[key] = ~~(rgb[key] / count); // https://stackoverflow.com/questions/5971645/what-is-the-double-tilde-operator-in-javascript
    });

    primary = `rgb(${rgb.r}, ${rgb.g}, ${rgb.b})`;
  }

  function handleDrop(e) {
    const { items } = e.dataTransfer;
    if (items[0] && items[0].type.match("^image/")) {
      const file = items[0].getAsFile();

      let reader = new FileReader();
      reader.readAsDataURL(file);
      reader.onloadend = () => {
        src = reader.result;
      };
    }
  }
</script>

<style>
  img {
    max-width: 100%;
    max-height: 50vh;
  }

  main {
    height: 100%;
    display: grid;
    justify-content: center;
    align-items: center;
  }

  .card {
    color: var(--secondary);
    text-align: center;
    padding: 1em;
    border-radius: 10px;
    box-shadow: 10px 10px 20px rgba(0, 0, 0, 0.2),
      -10px -10px 20px rgba(220, 220, 220, 0.1);
  }

  h1 {
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }
</style>

<main on:dragover|preventDefault on:drop|preventDefault={handleDrop}>
  <div class="card" style="--secondary: {secondary}">
    {#if src}
      <img {src} alt="your upload" bind:this={image} on:load={imageLoaded} />
    {/if}
    <canvas bind:this={canvas} style="display: none;" />
    <h1>Släpp en bild på mig!</h1>
  </div>
</main>
