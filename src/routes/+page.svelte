<script lang="ts">
    import { createClient } from "@supabase/supabase-js";
	import { PUBLIC_SUPABASE_URL, PUBLIC_SUPABASE_KEY } from "$env/static/public";
	const supabase = createClient(
		PUBLIC_SUPABASE_URL,
		PUBLIC_SUPABASE_KEY
	);

	let fileinput: any;
	let filename: string;
	let pic_url: any;
	let image: any;

	// using any for event e, as ts is not able to detect the type of file input event
	function onFileSelected(e: any) {
		image = e.target.files![0];
		filename = image.name;
		let reader = new FileReader(); 
		reader.readAsDataURL(image);

		reader.onload = (e) => {
			pic_url = e.target!.result;
		};
		
	};

	async function uploadImage() {
		console.log("Uploading Image");
		const { data, error } = await supabase
			.storage
			.from("images")
			.upload(`public/${filename}`, image, {
				cacheControl: "3600",
				upsert: false,
				contentType: "image/jpeg",
			})
		
		if (error) {
			console.log("Error uploading image", error);
		}
		
		if(data){
			console.log("Image uploaded successfully", data);
		}
	}	


</script>

<div id="app">
	<button
		class="upload-button"
		on:click={() => {
			fileinput.click();
		}}
	>
		<img
			class="upload"
			src="https://static.thenounproject.com/png/625182-200.png"
			alt=""
		/>
		<div>Choose Image</div>
		<input
			style="display:none"
			type="file"
			accept=".jpg, .jpeg, .png"
			on:change={(e) => onFileSelected(e)}
			bind:this={fileinput}
		/>
	</button>

	<hr />
	<br />

	<div id="preview">
		{#if pic_url}
			<p>Image Preview</p>
			<img src="{pic_url}" alt="" width="200px"/><br />
			<button on:click={uploadImage}>Upload</button>
		{/if}
		
	</div>
</div>

<style>
	#app {
		display: flex;
		align-items: center;
		justify-content: center;
		flex-flow: column;
	}

	#preview {
		display: flex;
		align-items: center;
		justify-content: center;
		flex-flow: column;
	}

	.upload {
		display: flex flex;
		height: 50px;
		width: 50px;
		cursor: pointer;
	}
	.upload-button {
		border: none;
		background: none;
	}
</style>
