<script lang="ts">
	import Awning from '$lib/Awning.svelte';
	import Callout from '$lib/Callout.svelte';
	import LinkOptions from '$lib/LinkOptions.svelte';
import Option from '$lib/Option.svelte';
	import ValueOptions from '$lib/ValueOptions.svelte';
    let why = $state<boolean>(false)
    let active = $state<string|null>("bluesky")
    let input = $state<string|null>(null)
    let action = $derived(() => {
        const login = primaryLogins.find(login => login.value === active)
        if (!login) return ''
        return login.action
    })
    interface PrimaryLogin {
        value: string;
        text: string;
        icon: string;
        action: string;
    }
    interface SecondaryLogin {
        value: string;
        text: string;
        icon: string;
        tooltip?: string;
    }
	const primaryLogins = $state<PrimaryLogin[]>([
		{
            value: 'bluesky',
			text: 'bluesky',
			icon: 'bluesky',
            action: 'blueskyLogin',
		},
        {
            value: 'email',
			text: 'email',
			icon: 'envelope-fill',
            action: 'blueskyLogin',
		}
	]);
    const secondaryLogins = $state<SecondaryLogin[]>([
		{
            value: 'google',
			text: 'google',
			icon: 'google',
		},
        {
            value: 'twitter',
			text: 'twitter',
			icon: 'twitter',
            tooltip: '(not known by any other name)'
		}, {
            value: 'reddit',
			text: 'reddit',
			icon: 'reddit',
		}
	]);
    interface LoginActions {
        [key: string]: (...args: any[]) => Promise<void>
    }
    const loginActions: LoginActions = {
        blueskyLogin: async (handle: string) => {
		console.log('');
		console.log(window.origin);
		if (!handle) return;
		const idk = await fetch('http://127.0.0.1:3000/proto/login', {
			method: 'POST',
			headers: {
				'Content-Type': 'application/json'
			},
			body: JSON.stringify({
				handle: handle
			})
		});
		const redirect = (await idk.json())['redirectUrl'];
		window.location.href = redirect;
	}
    }
</script>
<style>
    .at::after {
        margin-block: 4px;
        margin-right: 10px;
        content: "";
        border-right: 1px solid;
        border-color: var(--text);
    }
</style>
<div class="w-full h-full flex justify-center items-center" id="login-container">
	<div class="flex w-full max-w-sm flex-col gap">
    <Awning direction="left">log on with</Awning>
        <ValueOptions options={primaryLogins} bind:value={active} direction="left" />
        <div class="bg-secondary p-2.5 flex gap">
        <div class = "bg-background flex grow" id="login-input">
        <div class="size-10 flex justify-center items-center"><i class="bi bi-at"></i></div>
        <div class="at flex py-2"></div>
			<input bind:value={input} class="grow" />
        </div>
			<button class="bg-primary p-1.25" onclick={() => loginActions[action()](input)}>enter</button>
		</div>
        <Awning direction="right">or</Awning>
        <div class="flex"><Option icon="question-lg" text="why" direction="right" action={() => why = !why} relevant={why}></Option><LinkOptions options={secondaryLogins} direction="right"/></div>
        <div class={["flex flex-col gap", why ? "" : "hidden"]}><Awning direction="left">Good Question!</Awning>
        <Callout icon="brush-fill">
        You need to log on to <strong>order commissions</strong> or <strong>buy comix.</strong> It helps keep track of stuff.
        </Callout>
        <Callout icon="bookmark-fill">
        When you log on with <strong>Bluesky</strong>, you can <strong>like and bookmark posts.</strong>
        </Callout>
        <Callout icon="shield-fill">
        This site uses <strong>OAuth</strong>, so <strong>no personal or sensitive data is collected.</strong> No need to worry about a data breach.
        </Callout></div>
    </div>
</div>