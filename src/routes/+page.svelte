<script lang="ts">
    import ImgUpload from "../img_upload.svelte";
    import ImageSlider from "../ImageSlider.svelte";
    import ShowImg from "../show_img.svelte";
    import { Loader2, Download, Copy, Palette } from 'lucide-svelte';
    import { Button } from "$lib/components/ui/button/index.js";
    
    let isImageUploaded = false;

    let uploadedFile: File | null = null;
    let uploadedImageSrc: string | null = null;

    let colorizedImageUrl: string | null = null;
    let colorizedImageBlob: Blob | null = null; 
    let isLoading = false;
    let errorMessage = "";
    let copyButtonText = 'Copy Image';

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
    <link rel="icon" href="/favicon.ico" />
</svelte:head>

<main class="w-screen h-screen gap-12 flex flex-col  items-center">
    <div class="text-center pt-4 font-display h-[25%] flex items-center justify-end flex-col">
        <h1 class="text-5xl  font-bold mb-4">Image Colorizer</h1>
        <p class="text-2xl">Add color to old family photos and historic images</p>
    </div>
    <div class="flex flex-col md:flex-row justify-center w-[50%] gap-4 items-center">
        <ImgUpload bind:showImage={isImageUploaded} bind:file={uploadedFile} bind:imageSrc={uploadedImageSrc} />
        {#if isImageUploaded}
            <Button disabled={isLoading} onclick={handleColorize} class="text-xl font-semibold  w-44 py-8 px-13 flex flex-col justify-center items-center cursor-pointer" variant="default">
                <span>Colorize</span>
                <!-- <Palette  class="w-24 h-24"/> -->
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