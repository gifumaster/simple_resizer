<script lang="ts">
import { onMount } from 'svelte';

let imageSrc = '';
let downloadLink = '';

function handleDrop(event: DragEvent) {
    event.preventDefault();
    const file = event.dataTransfer?.files[0];
    if (file && file.type.startsWith('image/')) {
        const reader = new FileReader();
        reader.onload = (e) => {
            const img = new Image();
            img.onload = () => {
                const canvas = document.createElement('canvas');
                canvas.width = 800;
                canvas.height = 600;
                const ctx = canvas.getContext('2d');
                
                const srcWidth = img.width;
                const srcHeight = img.height;
                
                // クロップする長方形の計算
                const cropWidth = srcWidth / 2; // 横の1/2を長辺とする
                const cropHeight = cropWidth * 3/4; // 4:3の比率で高さを計算
                
                // 中心点の計算（左端から短辺/2、上から1/2の位置）
                const centerX = srcHeight / 2; // 左端から短辺/2の位置
                const centerY = srcHeight / 2;
                
                // クロップ範囲の計算
                const cropX = centerX - (cropWidth / 2);
                const cropY = centerY - (cropHeight / 2);
                
                // クロップして800x600にリサイズ
                ctx?.drawImage(
                    img,
                    cropX, cropY, cropWidth, cropHeight, // ソース範囲
                    0, 0, canvas.width, canvas.height // 出力範囲
                );
                
                imageSrc = canvas.toDataURL('image/jpeg');
                downloadLink = imageSrc.replace('image/jpeg', 'image/octet-stream');
            };
            img.src = e.target?.result as string;
        };
        reader.readAsDataURL(file);
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
</script>

<div class="container mx-auto p-4 max-w-xl">
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
            <svg xmlns="http://www.w3.org/2000/svg" class="h-12 w-12 text-base-content/50" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16l4.586-4.586a2 2 0 012.828 0L16 16m-2-2l1.586-1.586a2 2 0 012.828 0L20 14m-6-6h.01M6 20h12a2 2 0 002-2V6a2 2 0 00-2-2H6a2 2 0 00-2 2v12a2 2 0 002 2z" />
            </svg>
            <p class="text-base-content/70">画像をドラッグ&ドロップしてください</p>
        </div>
    </div>

    {#if imageSrc}
        <div class="card bg-base-200 shadow-xl mt-4">
            <div class="card-body items-center text-center gap-6">
                <img src={imageSrc} alt="Preview" class="thumbnail rounded-lg shadow-md" />
                <div class="card-actions flex flex-col items-center gap-4 w-full">
                    <div class="stats shadow">
                        <div class="stat place-items-center">
                            <div class="stat-title">サイズ</div>
                            <div class="stat-value text-primary">800×600</div>
                            <div class="stat-desc">JPEG形式</div>
                        </div>
                    </div>
                    <a href={downloadLink}
                       download={new Date().toISOString() + '.jpg'}
                       class="btn btn-primary btn-wide gap-2">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" />
                        </svg>
                        ダウンロード
                    </a>
                </div>
            </div>
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
    height: 112px; /* 800x600の比率に合わせて調整 */
    object-fit: cover;
    display: block;
    margin-left: auto;
    margin-right: auto;
}
</style>
