<script>
	export let name;

	let primary = '#893B3B'

	let src
	let canvas
	let image

	$: document.body.style.background = primary

	$: {
		if(src){
			image.src = src

			// TODO: gör det här på image onload istället, som det är nu så blir det samma ibland

			const ctx = canvas.getContext('2d')
			ctx.drawImage(image, 0, 0, canvas.width, canvas.height)
			let imageData = ctx.getImageData(0, 0, canvas.width, canvas.height)

			const length = imageData.data.length

			console.log(length)

			let count = 0
			let rgb = {r: 0, g: 0, b: 0}
			for(let i = 0; i < length; i+=4){
				++count
				rgb.r += imageData.data[i]
				rgb.g += imageData.data[i+1]
				rgb.b += imageData.data[i+2]
			}

			console.log(count, rgb)

			Object.keys(rgb).forEach(key => {
				rgb[key] = ~~(rgb[key] / count) // https://stackoverflow.com/questions/5971645/what-is-the-double-tilde-operator-in-javascript
			})

			primary = `rgb(${rgb.r}, ${rgb.g}, ${rgb.b})`
		}
	}

	function handleDrop(e){
		const {items} = e.dataTransfer
		if(items[0] && items[0].type.match('^image/')){
			const file = items[0].getAsFile()
			
			let reader = new FileReader()
			reader.readAsDataURL(file)
			reader.onloadend = () => {
				src = reader.result
			}
		}
	}
</script>

<main
	on:dragover|preventDefault
	on:drop|preventDefault={handleDrop}
>
<article>
	<img {src} alt="your upload" bind:this={image}/>
	<canvas bind:this={canvas} style="display: none;" />
	<h1>Hello {name}!</h1>
	<p>Visit the <a href="https://svelte.dev/tutorial">Svelte tutorial</a> to learn how to build Svelte apps.</p>

	<input type="color" bind:value={primary} style="height: 50px;"/>
</article>
</main>

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

	article {
		text-align: center;
		padding: 1em;
		border-radius: 10px;
		box-shadow: 10px 10px 10px rgba(0, 0, 0, 0.2),
					-10px -10px 20px rgba(255, 255, 255, 0.1);
		/* margin: 0 auto; */
		/* width: 240px; */
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>