<script lang="ts">
    import ImgUpload from "../img_upload.svelte";
    import ImageSlider from "../ImageSlider.svelte";
    import ShowImg from "../show_img.svelte";
    import { Loader2, Download, Copy, Palette } from 'lucide-svelte';
    
    import { Button } from "$lib/components/ui/button/index.js";
    

    let isImageUploaded = $state(false);

    let uploadedFile = $state<File | null>(null);
    let uploadedImageSrc = $state<string | null>(null);

    let colorizedImageUrl = $state<string | null>(null);
    let colorizedImageBlob = $state<Blob | null>(null); 
    let isLoading = $state(false);
    let errorMessage = $state("");
    let copyButtonText = $state('Copy Image');

    $effect(() => {
        if (uploadedFile) {
            colorizedImageUrl = null;
            colorizedImageBlob = null;
            errorMessage = '';
        }
    })

    async function handleColorize() {
        if (!uploadedFile) {
            errorMessage = "Please upload a file first.";
            return;
        }

        isLoading = true;
        errorMessage = "";
        colorizedImageUrl = null;

        const formData = new FormData();
        formData.append("image_file", uploadedFile);

        try {
            // const backendBaseUrl = import.meta.env.VITE_BACKEND_URL;
        
            // const apiUrl = `${backendBaseUrl}/api/colorize`;
            const apiUrl = 'api/colorize';
            console.log("Sending request to:", apiUrl);
            const response = await fetch(apiUrl, {
                method: "POST",
                body: formData,
            });

            if (!response.ok) {
                const errorData = await response.json().catch(() => ({ detail: "An unknown error occurred." }));
                throw new Error(`API Error: ${response.status} ${response.statusText}. ${errorData.detail}`);
            }
            const imageBlob = await response.blob();

			colorizedImageBlob = imageBlob;
            colorizedImageUrl = URL.createObjectURL(imageBlob);

        } catch (error: any) {
            console.error("Failed to colorize image:", error);
            errorMessage = error.message || "Failed to connect to the server.";
        } finally {
            isLoading = false;
        }
    }

    async function copyImageToClipboard() {
		if (!colorizedImageBlob) return;

		try {
			const item = new ClipboardItem({ [colorizedImageBlob.type]: colorizedImageBlob });
			await navigator.clipboard.write([item]);

			copyButtonText = 'Copied!';
			setTimeout(() => {
				copyButtonText = 'Copy Image';
			}, 2000);
		} catch (error) {
			console.error('Failed to copy image:', error);
			copyButtonText = 'Failed to copy';
            setTimeout(() => {
				copyButtonText = 'Copy Image';
			}, 2000);
		}
	}

</script>

<svelte:head>
    <title>Image Colorizer</title>
    <meta name="description" content="Add color to old family photos and historic images using our AI-powered image colorization tool." />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="icon" href="icon.svg" />
</svelte:head>

<main class="w-screen h-screen gap-12 flex flex-col  items-center">
    <div class="text-center pt-4 font-display h-[25%] flex items-center justify-end flex-col">
        <h1 class="text-5xl  font-bold mb-4">Image Colorizer</h1>
        <p class="text-2xl">Add color to old family photos and historic images</p>
    </div>
    <div class="absolute top-0 right-0 p-4">
        <a aria-label="Github Link" href="https://github.com/vojtam/colorization-backend">
            <svg width="40" height="40" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg" transform="rotate(0 0 0)"><path d="M12 2.24902C6.51613 2.24902 2 6.70064 2 12.249C2 16.6361 4.87097 20.3781 8.87097 21.7329C9.3871 21.8297 9.54839 21.5071 9.54839 21.2813C9.54839 21.0555 9.54839 20.4103 9.51613 19.5393C6.74194 20.1845 6.16129 18.1845 6.16129 18.1845C5.70968 17.0555 5.03226 16.7329 5.03226 16.7329C4.12903 16.0877 5.06452 16.0877 5.06452 16.0877C6.06452 16.12 6.6129 17.12 6.6129 17.12C7.48387 18.6684 8.96774 18.2168 9.51613 17.9264C9.6129 17.2813 9.87097 16.8297 10.1613 16.5716C7.96774 16.3458 5.6129 15.4748 5.6129 11.6684C5.6129 10.5716 6.03226 9.70064 6.64516 9.02322C6.54839 8.79741 6.19355 7.76515 6.74194 6.37806C6.74194 6.37806 7.6129 6.11999 9.51613 7.41031C10.3226 7.18451 11.1613 7.05548 12.0323 7.05548C12.9032 7.05548 13.7742 7.15225 14.5484 7.41031C16.4516 6.15225 17.2903 6.37806 17.2903 6.37806C17.8387 7.73289 17.5161 8.79741 17.3871 9.02322C18.0323 9.70064 18.4194 10.6039 18.4194 11.6684C18.4194 15.4748 16.0645 16.3458 13.871 16.5716C14.2258 16.8942 14.5484 17.5393 14.5484 18.4426C14.5484 19.7974 14.5161 20.8619 14.5161 21.1845C14.5161 21.4426 14.7097 21.7329 15.1935 21.6361C19.129 20.3135 22 16.6039 22 12.1845C21.9677 6.70064 17.4839 2.24902 12 2.24902Z" fill="#343C54"/></svg>
        </a>
    </div>
    <div class="flex flex-col md:flex-row justify-center w-[50%] gap-4 items-center">
        <ImgUpload bind:showImage={isImageUploaded} bind:file={uploadedFile} bind:imageSrc={uploadedImageSrc} />
        {#if isImageUploaded}
            <Button disabled={isLoading} onclick={handleColorize} class="text-xl font-semibold w-44 py-8 px-13 flex flex-col justify-center items-center cursor-pointer" variant="default">
                <span>Colorize</span>
            </Button>
        {/if}

    </div>

    {#if isLoading}
        <div class="flex items-center justify-center w-full h-[200px]">
            <Loader2 class="animate-spin text-blue-600" size={48} />
        </div>
    {:else if errorMessage}
        <div class="text-red-500 text-xl p-4">{errorMessage}</div>
    {/if}

    {#if isImageUploaded && !colorizedImageUrl}
        <div class="flex items-center justify-center w-full h-[500px] ">
            <ShowImg emptyCaption="upload a grayscale image" imageSrc={uploadedImageSrc || ""} />
        </div>
    {/if}

    {#if isImageUploaded && colorizedImageUrl}
        <div class="w-[90%] md:w-[50%] h-[500px] shrink-0"> 
            <ImageSlider
                src1={uploadedImageSrc || ""}
                src2={colorizedImageUrl || ""}
                caption1="Original Image"
                caption2="Colorized Image"
                fit="contain"
                
                />

        </div>

        <div class="flex gap-4">
                    <a
                        href={colorizedImageUrl}
                        download="colorized-image.png"
                        class="inline-flex items-center justify-center whitespace-nowrap rounded-md text-sm font-medium ring-offset-background transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50 bg-primary text-primary-foreground hover:bg-primary/90 h-10 px-4 py-2"
                    >
                        <Download class="w-4 h-4 mr-2" />
                        Download Image
                    </a>
                    <Button variant="outline" class="cursor-pointer" onclick={copyImageToClipboard}>
                        <Copy class="w-4 h-4 mr-2" />
                        {copyButtonText}
                    </Button>
        </div>
    {/if} 


    <div class="grid px-8 pb-4 lg:grid-cols-3 gap-8  items-start w-full">
        <div class="h-[350px]">
            <ImageSlider
                src1={"birb_2.png"}
                src2={"color_birb_2.png"}
                caption1="Original Image"
                caption2="Colorized Image"
                fit="cover"/> 

        </div>
        <div class="h-[350px]">
            <ImageSlider
            src1={"prague.png"}
            src2={"color_prague.png"}
            caption1="Original Image"
            caption2="Colorized Image"
            fit="cover"/>

        </div>
        <div class="h-[350px]">
            <ImageSlider
            src1={"pipik.png"}
            src2={"color_pipik.png"}
            caption1="Original Image"
            caption2="Colorized Image"
            fit="cover"/>
        </div>

    </div>
</main>