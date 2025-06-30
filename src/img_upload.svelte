<script lang="ts">
    import { FileImage } from "lucide-svelte";

    export let file: File | null = null;
	let input: HTMLInputElement | null = null;
    export let imageSrc: string | null = null;
    let filename: string = "No file selected";
	export let showImage: boolean = false;

    function onChange(): void {
        if (!input?.files) {
			return;
		}

        const selectedFile = input.files[0];
        if (selectedFile) {
            file = selectedFile;
            showImage = true;
            filename = file.name;
            const reader = new FileReader();
            reader.addEventListener('load', () => {
                if (typeof reader.result === 'string') {
                    imageSrc = reader.result;
                } else {
                    console.error("FileReader result is not a string");
                }
            });
            reader.readAsDataURL(file);

            return ;
        }
        file = null;
        showImage = false;
        filename = "No file selected";
    }
</script>

<div class="flex flex-col items-center gap-2">
	<input
		type="file"
		accept="image/*"
		bind:this={input}
		on:change={onChange}
		id="file-upload"
		class="sr-only"
	/>

	<label
		for="file-upload"
		class="cursor-pointer p-3 text-center flex justify-center flex-col text-xl items-center rounded-md bg-blue-600 hover:bg-indigo-600  font-semibold text-white shadow-sm focus-visible:outline-offset-2 focus-visible:outline-indigo-600 transition-colors"
	>   
		<span class="p-2">Upload an image</span>
        <FileImage class="" />
	</label>

	<!-- <p class="text-sm text-gray-500">{filename}</p> -->
</div>

