<script lang="ts">
	import type { Snippet } from "svelte";

    let { children, icon }: { children: Snippet, icon: string } = $props()
</script>

<div class="p-2.5 bg-secondary flex w-full gap">
<div class="flex justify-center items-center">
<div class="icon flex-none"><i class={"bi bi-" + icon}></i></div>
</div>
<div class="">
{@render children()}
</div>
</div>