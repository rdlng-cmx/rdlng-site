<script lang="ts">
	import type { MouseEventHandler } from "svelte/elements";

	interface Props {
        brief?: boolean;
		icon: string;
		text: string;
        tooltip?: string;
		active?: boolean;
        direction?: "right"|"left"
		relevant?: boolean | (() => boolean);
		action: MouseEventHandler<HTMLButtonElement>
	}
	let { active = true, relevant = false, action, text, icon, brief = false, tooltip, direction = "left" }: Props = $props();
	const isRelevant = $derived(() => {
        if (typeof relevant === "boolean" || typeof relevant === "undefined")  return relevant && active ? true : false
        else return relevant()
        });
</script>

<button
	class={['option', active ? 'active' : '', isRelevant() ? 'relevant' : '', brief ? "brief" : "", "flex", direction]}
	type="button"
	onclick={action}
    title={tooltip}
>
	<div class="icon">
		<i class={['bi', 'bi-' + icon]}></i>
	</div>
	<div class={["text", direction]}>{text}</div>
</button>

<style>
.option.left {
    flex-direction: row;
}
.option.right {
    flex-direction: row-reverse;
}
.relevant {
  background: var(--primary);
}
.option.active:not(.relevant) .text {
  transition-delay: 0.2s;
}

.option:not(.brief) {
&.active:hover, &.relevant, &.show  {
  & .text {
  width: calc-size(fit-content, size);
  opacity: 1;
  &.left {
    padding-right: 10px;
  }
  &.right {
    padding-left: 10px;
  }
  }
}}

@keyframes show-text {
  0% {
    width: 0;
    opacity: 0;
    padding-right: 0px;
  }
  10% {
    width: calc-size(fit-content, size);
    opacity: 1;
    padding-right: 10px;
  }
  90% {
    width: calc-size(fit-content, size);
    opacity: 1;
    padding-right: 10px;
  }
  100% {
    width: 0;
    opacity: 0;
    padding-right: 0px;
  }
}

.brief.option.relevant, .brief.option.relevant:active {
    & .text {
      animation-name: show-text;
      animation-duration: 2s;
    }
}

</style>
