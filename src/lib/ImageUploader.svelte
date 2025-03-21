<script lang="ts">
import { onMount } from 'svelte';

let images: { src: string; downloadLink: string; outputSize: number }[] = [];
let fileInput: HTMLInputElement;

function processImage(file: File) {
    if (file && file.type.startsWith('image/')) {
        const reader = new FileReader();
        reader.onload = (e) => {
            const img = new Image();
            img.onload = () => {
                const srcWidth = img.width;
                const srcHeight = img.height;
                
                const outputSize = (srcWidth <= 1920 && srcHeight <= 1024) ? 1024 : 2048;
                
                const canvas = document.createElement('canvas');
                canvas.width = outputSize;
                canvas.height = outputSize;
                const ctx = canvas.getContext('2d');
                
                const cropSize = Math.min(srcWidth, srcHeight);
                const centerX = srcHeight / 2;
                const centerY = srcHeight / 2;
                const cropX = centerX - (cropSize / 2);
                const cropY = centerY - (cropSize / 2);
                
                ctx?.drawImage(
                    img,
                    cropX, cropY, cropSize, cropSize,
                    0, 0, canvas.width, canvas.height
                );
                
                const imageSrc = canvas.toDataURL('image/jpeg');
                const downloadLink = imageSrc.replace('image/jpeg', 'image/octet-stream');

                // 画像を配列に追加
                if (images.length >= 10) {
                    images = images.slice(1); // 最初の画像を削除
                }
                images = [...images, { src: imageSrc, downloadLink, outputSize }];
            };
            img.src = e.target?.result as string;
        };
        reader.readAsDataURL(file);
    }
}

function handleDrop(event: DragEvent) {
    event.preventDefault();
    const file = event.dataTransfer?.files[0];
    if (file) {
        processImage(file);
    }
}

function handleFileSelect(event: Event) {
    const target = event.target as HTMLInputElement;
    const file = target.files?.[0];
    if (file) {
        processImage(file);
    }
}

function handleDragOver(event: DragEvent) {
    event.preventDefault();
}

let isHovered = false;

function handleDragEnter() {
    isHovered = true;
}

function handleDragLeave() {
    isHovered = false;
}

function removeImage(index: number) {
    images = images.filter((_, i) => i !== index);
}
</script>

<div class="container mx-auto p-4 max-w-7xl">
    <div
        class="dropzone card bg-base-200 shadow-xl {isHovered ? 'border-primary' : ''}"
        role="button"
        tabindex="0"
        on:drop={handleDrop}
        on:dragover={handleDragOver}
        on:dragenter={handleDragEnter}
        on:dragleave={handleDragLeave}
    >
        <div class="card-body items-center text-center">
            <p class="text-base-content/70">画像をドラッグ&ドロップ、または選択してください</p>
            <label class="btn btn-outline btn-sm mt-2 border-primary">
                ファイルを選択
                <input
                    type="file"
                    accept="image/*"
                    class="hidden"
                    on:change={handleFileSelect}
                    bind:this={fileInput}
                />
            </label>
        </div>
    </div>

    {#if images.length > 0}
        <div class="grid grid-cols-5 gap-4 mt-4">
            {#each images as image, i}
                <div class="card bg-base-200 shadow-xl">
                    <div class="card-body items-center text-center gap-4">
                        <img src={image.src} alt="Preview" class="thumbnail rounded-lg shadow-md" />
                        <div class="card-actions flex flex-col items-center gap-2 w-full">
                            <div class="flex gap-2">
                                <a href={image.downloadLink}
                                   download={new Date().toISOString() + '.jpg'}
                                   class="btn btn-primary btn-sm inline-flex items-center">
                                    <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-1" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" />
                                    </svg>
                                    ダウンロード
                                </a>
                                <button class="btn btn-error btn-sm inline-flex items-center" on:click={() => removeImage(i)} aria-label="Delete image">
                                    <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
                                    </svg>
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
            {/each}
        </div>
    {/if}
</div>

<style>
.dropzone {
    min-height: 200px;
    border: 2px dashed currentColor;
    transition: all 0.3s ease;
}

.dropzone:hover {
    border-color: hsl(var(--p));
}

.thumbnail {
    width: 150px;
    height: 150px;
    object-fit: cover;
    display: block;
    margin-left: auto;
    margin-right: auto;
}

.hidden {
    display: none;
}
</style>
