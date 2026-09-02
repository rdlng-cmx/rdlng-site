<script lang="ts">
	import { goto } from "$app/navigation";
	import Option from "./Option.svelte";

    interface OptionData {
      icon: string,
      text: string,
      value: string,
      tooltip?: string
    }
    interface Props {
        options: OptionData[],
        direction?: "left"|"center"|"right"
    }
    let { options, direction = "center" }: Props = $props()
</script>
<div class={["flex menu-row items-center w-full", direction === "left" ? "justify-start" : direction === "center" ? "justify-center" : "justify-end"]}>
{#each options as option}
    <Option text={option.text} tooltip={option.tooltip} icon={option.icon} action={() => goto(option.value)} direction={direction !== "center" ? direction === "right" ? "right" : "left" : "left"}/>
{/each}
</div>