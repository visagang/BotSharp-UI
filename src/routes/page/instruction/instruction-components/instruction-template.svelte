<script>
    import { createEventDispatcher, onMount } from 'svelte';
	import { _ } from 'svelte-i18n';
    import Select from '$lib/common/Select.svelte';

    const svelteDispatch = createEventDispatcher();

    /** @type {import('$agentTypes').AgentModel[]} */
    export let agents = [];

    /** @type {boolean} */
    export let disabled = false;

    /** @type {import('$commonTypes').LabelValuePair[]} */
    let agentOptions = [];
    /** @type {any[]} */
    let templateOptions = [];

    /** @type {import('$agentTypes').AgentModel | null | undefined} */
    let selectedAgent = null;
    /** @type {any} */
    let selectedTemplate = null;

    $: {
        collectAgentOptions(agents);
    }

    /**
	 * @param {import('$agentTypes').AgentModel[]} agents
	 */
    function collectAgentOptions(agents) {
        agentOptions = agents?.filter(x => x.templates?.length > 0).map(x => ({
            label: x.name,
            value: x.id
        }))?.sort((a, b) => a.label.localeCompare(b.label)) || [];
    }

    /** @param {any} e */
    function selectAgent(e) {
        // @ts-ignore
		const selectedValues = e.detail.selecteds?.map(x => x.value) || [];
        selectedTemplate = null;
        selectedAgent = selectedValues.length > 0 ? agents?.find(x => x.id === selectedValues[0]) : null;
        templateOptions = selectedAgent?.templates?.map(x => ({
            id: x.name,
            name: x.name,
            label: x.name,
            value: x.name,
            content: x.content
        })) || [];

        dispatchEvent();
    }

    /** @param {any} e */
    function selectTemplate(e) {
        // @ts-ignore
		const selectedValues = e.detail.selecteds?.map(x => x.value) || [];
        selectedTemplate = selectedValues.length > 0 ? templateOptions.find(x => x.value === selectedValues[0]) : null;
        dispatchEvent();
    }

    function dispatchEvent() {
        svelteDispatch('agentSelected', {
            agent: selectedAgent || null,
            template: selectedTemplate || null
        });
    }
</script>


<div class="instruct-setting-section instruct-setting-padding">
    <div class="instruct-setting-item">
        <div class="instruct-setting-dropdown">
            <div class="text-primary fw-bold mb-1">Agent</div>
            <!-- <select class="form-select" id="agent" value={selectedAgent?.id || null} disabled={disabled} on:change={e => selectAgent(e)}>
                <option value={null}>{$_('Select Agent')}</option>
                {#each agentOptions as op}
                    <option value={`${op.id}`} selected={op.id === selectedAgent?.id}>{$_(`${op.name}`)}</option>
                {/each}
            </select> -->
            <Select
                tag={'agent-select'}
                placeholder={'Select Agent'}
                searchMode
                disabled={disabled}
                selectedValues={selectedAgent?.id ? [selectedAgent.id] : []}
                options={agentOptions}
                on:select={e => selectAgent(e)}
            />
        </div>
    </div>

    <div class="instruct-setting-item">
        <div class="instruct-setting-dropdown">
            <div class="text-primary fw-bold mb-1">Template</div>
            <!-- <select class="form-select" id="template" value={selectedTemplate?.id || null} disabled={disabled} on:change={e => selectTemplate(e)}>
                <option value={null}>{$_('Select Template')}</option>
                {#each templateOptions as op}
                    <option value={`${op.id}`} selected={op.id === selectedTemplate?.id}>{$_(`${op.name}`)}</option>
                {/each}
            </select> -->
            <Select
                tag={'template-select'}
                placeholder={'Select Template'}
                searchMode
                disabled={disabled}
                selectedValues={selectedTemplate?.id ? [selectedTemplate.id] : []}
                options={templateOptions}
                on:select={e => selectTemplate(e)}
            />
        </div>
    </div>
</div>