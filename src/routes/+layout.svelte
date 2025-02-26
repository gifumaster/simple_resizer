<script lang="ts">
	import '../app.css';
	let { children } = $props();
</script>

<div class="min-h-screen bg-base-100">
    <header class="navbar bg-base-200 shadow-lg">
        <div class="flex-1">
            <a class="btn btn-ghost normal-case text-xl">Simple Resizer</a>
        </div>
    </header>

    <main class="py-8">
        {@render children()}
    </main>

    <footer class="footer footer-center p-4 bg-base-200 text-base-content">
        <div>
            <p>アバターサムネ用に画像を編集</p>
        </div>
    </footer>
</div>
