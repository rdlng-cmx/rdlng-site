<script lang="ts">
    import type { Snippet } from 'svelte';

	// Accept the implicit children prop
	let { children, direction = "center" }: { children: Snippet, direction?: "left"|"center"|"right" } = $props();
</script>
<style>
.line {
    border-bottom: solid 1px var(--secondary);
    
}    
</style>
<div class="flex items-center justify-center gap">
{#if direction !== "left"}
    <div class="line grow"></div>
{/if}
<div ><h2>{@render children()}</h2></div>
{#if direction !== "right"}
    <div class="line grow"></div>
{/if}
</div>