<script>
    import { Card, CardBody, CardTitle, Col, Row } from '@sveltestrap/sveltestrap';
    import { GetDialogs } from '$lib/services/conversation-service.js';
    import { format } from '$lib/helpers/datetime';
    import { onMount } from 'svelte';

    /** @type {import('$types').ChatResponseModel[]} */
    let dialogs = [];

    /** @type {import('$types').ConversationModel} */
    export let conversation;

    onMount(async () => {
        dialogs = await GetDialogs(conversation.id);
    });

    /** 
     * @param {import('$types').ChatResponseModel} dialog
     * @returns {boolean}
    */
    function showInRight(dialog) {
        const sender = dialog.sender;
        return sender.role != "client" && sender.role != "user";
    }
</script>

<Card>
    <CardBody>
        <CardTitle class="mb-5 h4">Dialogs</CardTitle>
        <div class="">
            <ul class="verti-timeline list-unstyled">
                {#each dialogs as dialog}
                <li class="event-list">
                    <div class="event-timeline-dot">
                      <i
                        class={"bx " + showInRight(dialog)
                          ? "bx-right-arrow-circle bx-fade-right"
                          : "bx-right-arrow-circle"}
                      />
                    </div>
                    <div class="d-flex">
                        <div class="flex-shrink-0 me-3">
                            <i class={"bx " + (showInRight(dialog) ? "bx-user" : "bx-bot") + " h2 text-primary"}></i>
                        </div>
                        <div class="flex-grow-1">
                            <div>
                                <span>{dialog.sender.full_name}</span>
                                <p class="fw-bold">{dialog.text}</p>
                                <span class="text-muted">{format(dialog.created_at, 'long-time')}</span>
                            </div>
                        </div>
                    </div>
                  </li>
                {/each}
            </ul>
        </div>
    </CardBody>
</Card>