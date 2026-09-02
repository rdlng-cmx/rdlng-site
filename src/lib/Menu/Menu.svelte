<script lang="ts">
    import './hamburger.css'
    import gsap from 'gsap'
    import { page } from '$app/state';
    import { goto } from '$app/navigation';
	import Option from '$lib/Option.svelte';
    interface Option {
      location: string,
      icon: string,
      text: string,
      subroutes?: Option[]
    }
    let active = $state(false)
    let hamburgerActive = $state(true)
    let optionsActive = $state(false)
    let old = $state("")
    let innerWidth = $state(0)
    let mobile = $derived(() => innerWidth < 480)

    const onStart = () => {
      const media = gsap.matchMedia()
      media.add("(min-width: 481px)", () => {
        if (menuOptions) {
          gsap.set(menuOptions, {visibility: "hidden"})
          gsap.set(menuOptions.children, { x: -100, opacity: 0, scale: 0.9})
          gsap.set(`#menu-options-2 > div > button`, { x: -100, opacity: 0, scale: 0.9})
        }
        })
        media.add("(max-width: 480px)", () => {
        if (menuOptions) {
          gsap.set(menuOptions, {visibility: "hidden"})
          gsap.set(menuOptions.children, { y: -100, opacity: 0, scale: 0.9})
          gsap.set(`#menu-options-2 > div > button`, { y: 100, opacity: 0, scale: 0.9})
        }
        })
    }
    $effect(onStart)
    let menuOptions = $state<HTMLDivElement | null>(null);
    let menuSuboptions = $state<HTMLDivElement | null>(null);
    const easing = (bool: boolean) => ({
      duration: 0.2,
      stagger: bool ? -0.01 : 0.01,
      ease: "power1.out",
    })
    const showTextAnimationMobile = (element: HTMLElement) => {
      if (!menuOptions ) return; 
      const timeline = gsap.timeline();
      timeline.call(
        () => {
          element.classList.add('show');
        })
      .call(() => {
          element.classList.remove('show');
        }, [], 5)
    }
    const openAnimationsDesktop = () => {
      if (!menuOptions ) return; 
      const timeline = gsap.timeline();
        timeline
        .call(() => hamburgerActive = false)
        .set(menuOptions, {visibility: "visible"})
        .to(menuOptions.children, {
          opacity: 1,
          x: 0,
          scale: 1,
          ...easing(true)
        }, ">")
        
        .call(() => optionsActive = true, [], ">")
        .call(() => hamburgerActive = true, [], ">")
        return timeline
      }
    const closeAnimationsDesktop = () => {
      if (!menuOptions ) return; 
      const timeline = gsap.timeline();
        timeline
        .call(() => hamburgerActive = false)
        .call(() => optionsActive = false)
        .to(menuOptions.children, {
          opacity: 0,
          x: -100,
          scale: 0.9,
          ...easing(false)
        }, '<')
        .set(menuOptions, {visibility: "hidden"}, '>')
        .call(() => hamburgerActive = true, [], ">")
        return timeline
      }
    const openAnimationsMobile = () => {
      if (!menuOptions ) return; 
      const timeline = gsap.timeline();
        timeline
        .call(() => hamburgerActive = false)
        .set(menuOptions, {visibility: "visible"})
        .to(menuOptions.children, {
          opacity: 1,
          y: 0,
          scale: 1,
          ...easing(true)
        }, ">")
        
        .call(() => optionsActive = true, [], ">")
        .call(() => hamburgerActive = true, [], ">")
        return timeline
      }
    const closeAnimationsMobile = () => {
      if (!menuOptions ) return; 
      const timeline = gsap.timeline();
        timeline
        .call(() => hamburgerActive = false)
        .call(() => optionsActive = false)
        .to(menuOptions.children, {
          opacity: 0,
          y: -100,
          scale: 0.9,
          ...easing(false)
        }, '<')
        .set(menuOptions, {visibility: "hidden"}, '>')
        .call(() => hamburgerActive = true, [], ">")
        return timeline
      }
    const toggleMenu = () => {
      if (!menuOptions || !hamburgerActive) return;
      active = !active

      const media = gsap.matchMedia();
      if (active === true) {
        media.add("(max-width: 480px)", () => {
          openAnimationsMobile()
          openSubmenuAnimationsMobile(page.url.pathname.split("/")[1])
        })
        media.add("(min-width: 481px)", () => {
          openAnimationsDesktop()
          openSubmenuAnimationsDesktop(page.url.pathname.split("/")[1])
        })
      }
      else {
        media.add("(max-width: 480px)", () => {
          closeAnimationsMobile()
          closeSubmenuAnimationsMobile(true)
        })
        media.add("(min-width: 481px)", () => {
          closeAnimationsDesktop()
          closeSubmenuAnimationsDesktop(true)
        })
      }
    }

    const options: Option[] = $state([
        {
            location: "/",
            icon: "house-door-fill",
            text: "home"
        },
        {
          location: "/works",
          icon: "paperclip",
          text: "works",
          subroutes: [
            {
              location: "/art",
              text: "art",
              icon: "image-alt"
            },
            {
              location: "/plugins",
              text: "plugins",
              icon: "plugin"
              
            },
            {
              location: "/comix",
              text: "comix",
              icon: "chat-fill"
            }
          ]
        },
        {
            location: "/comms",
            icon: "brush-fill",
            text: "comms"
        },
        {
            location: "/socials",
            icon: "chat-square-heart-fill",
            text: "socials"
        },
        {
            location: "/login",
            icon: "key-fill",
            text: "login"
        }
    ])
    const relevant = $derived(() => options.find(option => option.location === "/" + page.url.pathname.split("/")[1]))

    const openSubmenuAnimationsDesktop = (menu: string) => {
      if (!menuSuboptions ) return gsap.timeline(); 
      const timeline = gsap.timeline();
        timeline
        .set(`#suboptions-${menu}`, { visibility: "visible"})
        .to(`#suboptions-${menu} > button`, {
          opacity: 1,
          x: 0,
          scale: 1,
          ...easing(true)
        }, ">")
        return timeline
      }
    const openSubmenuAnimationsMobile = (menu: string) => {
      if (!menuSuboptions ) return gsap.timeline(); 
      const timeline = gsap.timeline();
        timeline
        .set(`#suboptions-${menu}`, { visibility: "visible"})
        .to(`#suboptions-${menu} > button`, {
          opacity: 1,
          y: 0,
          scale: 1,
          ...easing(true)
        }, ">")
        return timeline
      }
    const closeSubmenuAnimationsDesktop = (bool: boolean = false) => {
      console.log(old)
      if (!menuSuboptions ) return gsap.timeline(); 
      const timeline = gsap.timeline();
        timeline
        .to(`#suboptions-${bool ? page.url.pathname.split("/")[1] : old} > button`, {
          opacity: 0,
          scale: 0.9,
          x: -100,
          ...easing(false)
        }, '>')
        .set(`#suboptions-${bool ? page.url.pathname.split("/")[1] : old}`, { visibility: "hidden"})
        return timeline
      }
      const closeSubmenuAnimationsMobile = (bool: boolean = false) => {
      console.log(old)
      if (!menuSuboptions ) return gsap.timeline(); 
      const timeline = gsap.timeline();
        timeline
        .to(`#suboptions-${bool ? page.url.pathname.split("/")[1] : old} > button`, {
          opacity: 0,
          scale: 0.9,
          y: 100,
          ...easing(false)
        }, '>')
        .set(`#suboptions-${bool ? page.url.pathname.split("/")[1] : old}`, { visibility: "hidden"})
        return timeline
      }
    const refreshSubmenu = () => {
      const media = gsap.matchMedia()
      const menu = page.url.pathname.split("/")[1]
      media.add("(min-width: 481px)", () => {
      const timeline = gsap.timeline()
      timeline
        .add(closeSubmenuAnimationsDesktop())
        .add(openSubmenuAnimationsDesktop(menu))
      return timeline
      })
      media.add("(max-width: 480px)", () => {
      const timeline = gsap.timeline()
      timeline
        .add(closeSubmenuAnimationsMobile())
        .add(openSubmenuAnimationsMobile(menu))
      return timeline
      })
      console.log(menu)
    }
</script>

<style>
nav {
  margin: 0;
  padding: 0;
  height: 100%;
  display: flex;
  gap: 10px;
  flex-direction: column;
}
.menu-options {
  visibility: hidden;
  top: 100%;
  display: flex;
  flex-direction: row;
  gap: 10px;
  transition: all 0.2s allow-discrete;
}
.menu-row {
  position: relative;
  display: flex;
  flex-direction: row;
  width: fit-content;
  gap: 10px;
}

@media screen and (max-width: 480px) {
  #menu {
    flex-direction: column;
    width: 40px;
    justify-content: space-between;
    height: 100%;
    top: 0;
    left: 0;
  }
  .menu-options {
    flex-direction: column;
  }
  .menu-row {
    flex-direction: column;
  }
}
</style>
<svelte:window bind:innerWidth/>
{#snippet suboption(option: Option)}
<Option icon={option.icon} text={option.text} active={optionsActive} brief={mobile()} relevant={() => optionsActive && relevant()?.location + option.location === page.url.pathname} action={
(event) => {
  goto(relevant()?.location + option.location)
  showTextAnimationMobile(event.currentTarget)
}
}>
</Option>
{/snippet}
{#snippet option(option: Option)}
<Option icon={option.icon} text={option.text} active={optionsActive} brief={mobile()} relevant={() => optionsActive && relevant()?.location === option.location} action={() => {
  if (page.url.pathname === option.location) return;
  old = page.url.pathname.split("/")[1]
  goto(option.location).then(() => {
    if (old === option.location) return;
    refreshSubmenu()
  })
  }}>
      </Option>
{/snippet}
<nav id="menu" class={optionsActive ? "active" : ""}>
    <div class="menu-row">
    <div class="option" id="menu-open">
    <button type="button" aria-label="menu" class={["hamburger hamburger--minus icon", active ? "is-active" : ""]} onclick={toggleMenu}>
        <span class="hamburger-inner"></span>
    </button>
    </div>
    <div class="menu-options" id="menu-options-1" bind:this={menuOptions}>
    {#each options as item}
        {@render option(item)}
    {/each}
    </div>
    </div>
    <div class="menu-row">
      <div id="menu-options-2" bind:this={menuSuboptions}>
    {#each options as route}
    <div class="menu-options" id={"suboptions-" + route.text}>
        {#each route.subroutes ?? [] as subroute}
          {@render suboption(subroute)}
        {/each}
    </div>
    {/each}
    </div>
    </div>
  </nav>
