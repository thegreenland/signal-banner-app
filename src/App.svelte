<script>
    import { onMount } from 'svelte';
    import Header from './components/Header.svelte';
    import FileUpload from './components/FileUpload.svelte';
    import ImageCropModal from './components/ImageCropModal.svelte';

    let selectedFile = null;
    let previewUrl = '';
    let error = '';
    let mergedImage = null;
    let isEditing = false;

    let unique = {}

    function handleFileSelected(event) {
        selectedFile = event.detail.file;
        previewUrl = event.detail.previewUrl;
        error = '';
        isEditing = true;
    }

    function handleError(event) {
        error = event.detail;
    }

    function handleProcessedImage(event) {
        mergedImage = event.detail.mergedImage; // Get the merged image from the event
        isEditing = false;
    }

    function downloadImage() {
        const link = document.createElement('a');
        link.href = mergedImage;
        link.download = 'profile_with_banner.jpg';
        link.click();
    }

    function reEdit() {
        isEditing = true; // Reopen the modal for editing
    }

    export function resetApp() {
        unique = {}
        selectedFile = null; // Clear the selected file
        previewUrl = ''; // Clear the preview URL
        error = ''; // Clear any errors
        mergedImage = null; // Clear the merged image
        isEditing = false; // Close the modal
    }

</script>

<style>    
    .merged-image-container {
        text-align: center;
        margin-top: 20px;
    }

    .merged-image {
        object-fit: cover; /* Ensure the image fits the container */
        width: 300px;
        height: 300px;
        border: 1px solid #ccc;
        border-radius: 8px;
    }
</style>

<main class="flex flex-col items-center justify-center min-h-screen max-h-screen ">
    <div class="flex flex-col w-full max-w-4xl p-8 items-center rounded-xl shadow-lg bg-base-200">
        <div class="w-full mb-6">
            <Header title="TGL Signal Banner Tool" />
        </div>
        <div class="flex flex-col md:flex-row items-center min-h-[60vh] w-full">
            <div class="flex flex-col flex-[3] p-8 items-center max-w-4xl w-full h-full">
                <div class="container w-full max-w-xl">
                    <div class="flex flex-col justify-center card-body">
                        
                        {#if !mergedImage}
                        <h2 class="card-title">Upload je foto om te starten:</h2>
                            {#key unique}
                                <FileUpload 
                                    on:fileSelected={handleFileSelected}
                                    on:error={handleError}
                                />
                            {/key}
                        {/if}

                        {#if isEditing && previewUrl}
                            <ImageCropModal 
                                {previewUrl}
                                on:error={handleError}
                                on:imageProcessed={handleProcessedImage}
                            />
                        {/if}

                        {#if error}
                            <div class="alert alert-error shadow-lg mt-4">
                                <div>
                                    <svg xmlns="http://www.w3.org/2000/svg" class="stroke-current flex-shrink-0 h-6 w-6" fill="none" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2m7-2a9 9 0 11-18 0 9 9 0 0118 0z" /></svg>
                                    <span>{error}</span>
                                </div>
                            </div>
                        {/if}
                    

                    {#if mergedImage}
                        <div class="flex flex-col items-center merged-image-container">
                            <h3 class="font-bold text-lg mb-4">Done!</h3>
                            <img src={mergedImage} alt="Merged Image" class="merged-image" />
                            <div class="flex flex-col gap-4 w-full max-w-xs mt-4">
                                <button class="btn btn-primary" on:click={downloadImage}>
                                    Download foto
                                </button>
                                <button class="btn btn-error" on:click={reEdit}>
                                    Verder aanpassen
                                </button>
                                <button class="btn btn-secondary" on:click={resetApp}>
                                    Reset
                                </button>
                            </div>
                        </div>
                    {/if} 
                </div>
            </div>
        </div>
        <div class="flex-1 flex flex-col h-full">
            <div class="card w-full h-full shadow-sm">
                <div class="card-body card-border flex flex-col justify-between h-full">
                    <p class="text-emerald-500">
                        <span class="font-bold">
                            TEST: Maak een kleine vuist tegen de uitbuiting van ons digitale leven door Big Tech! 
                            <br><br>
                        </span>
                        <span class="italic">- "Maar heeft dat zin dan?"</span> <br><br> Absoluut - de waarde van sociale media platforms wordt alleen bepaald door het aantal gebruikers, dus we kunnen samen het tij keren. <br><br>Zie:
                        <a class="underline" href="https://en.wikipedia.org/wiki/Network_effect" target="_blank">https://en.wikipedia.org/wiki/Network_effect</a>
                    </p>
                </div>
            </div>
        </div> 
    </div>
    <div class="alert alert-info mt-4 w-full">
        <svg xmlns="http://www.w3.org/2000/svg" class="stroke-current shrink-0 h-6 w-6" fill="none" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M12 20a8 8 0 100-16 8 8 0 000 16z" />
        </svg>
        <span>
            NB: deze website is bewust ontworpen om volledig "client-side" te draaien om privacy te waarborgen; jouw foto wordt dus alleen lokaal in de browser geüpload en nergens anders opgeslagen. De publieke code staat op de 
            <a class="underline" href="https://github.com/thegreenland/signal-banner-app/" target="_blank">TGL GitHub</a>.
        </span>
    </div>
</main>