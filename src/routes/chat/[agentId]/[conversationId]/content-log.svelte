<script>
    import 'overlayscrollbars/overlayscrollbars.css';
    import { OverlayScrollbars } from 'overlayscrollbars';
	import { afterUpdate, onMount, tick } from 'svelte';
	import ContentLogElement from './content-log-element.svelte';

    /** @type {import('$types').ContentLogModel[]} */
    export let logs = [];

    /** @type {() => void} */
    export let closeWindow;

    // @ts-ignore
    let scrollbar;

    const options = {
		scrollbars: {
			visibility: 'auto',
			autoHide: 'move',
			autoHideDelay: 100,
			dragScroll: true,
			clickScroll: false,
			theme: 'os-theme-dark',
			pointers: ['mouse', 'touch', 'pen']
		}
	};

    onMount(async () => {
		const scrollElements = document.querySelectorAll('.content-log-scrollbar');
		scrollElements.forEach((item) => {
			scrollbar = OverlayScrollbars(item, options);
		});

		refreshLog();
	});

    afterUpdate(() => {
        refreshLog();
    });


    async function refreshLog() {
        // trigger UI render
        logs = logs || [];
        await tick();

        setTimeout(() => {
            const { viewport } = scrollbar.elements();
            viewport.scrollTo({ top: viewport.scrollHeight, behavior: 'smooth' }); // set scroll offset
        }, 200);
    }
     
</script>

<div class="chat-log">
    <div class="card mb-0 log-background" style="height: 100%;">
        <div class="log-close-btn padding-side ">
            <button
                type="button"
                class="btn btn-sm btn-secondary btn-rounded chat-send waves-effect waves-light"
                on:click={() => closeWindow()}
            ><i class="mdi mdi-window-close"></i>
            </button>
        </div>
        <div class="content-log-scrollbar log-list padding-side">
            <ul>
                {#each logs as log}
                    <ContentLogElement data={log} />
                {/each}
            </ul>
        </div>
    </div>
</div>