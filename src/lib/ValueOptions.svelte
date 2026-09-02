<script lang="ts">
	import Option from "./Option.svelte";

    interface OptionData {
      icon: string,
      text: string,
      value?: any
    }
    interface Props {
        options: OptionData[],
        value: any,
        direction?: "left"|"center"|"right"
    }
    
    let { options, value = $bindable(''), direction = "center" }: Props = $props()
</script>
<div class={["flex menu-row items-center w-full", direction === "left" ? "justify-start" : direction === "center" ? "justify-center" : "justify-end"]}>
{#each options as option}
    <Option text={option.text} icon={option.icon} action={() => value = option.value} relevant={() => option.value === value} direction={direction !== "center" ? direction === "right" ? "right" : "left" : "left"}/>
{/each}
</div>