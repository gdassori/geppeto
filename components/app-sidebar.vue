<script lang="ts" setup>
const {
    conversationList,
    createConversation,
    switchConversation,
    clearConversations,
} = useConversations()

const route = useRoute()
const { apiKey } = useSettings()

const conversationsSortedByUpdatedAt = computed(() => {
    if (conversationList.value === null) {
        return null
    }
    return conversationList.value.sort((a, b) => {
        if (a.updatedAt === null) {
            return 1
        }
        if (b.updatedAt === null) {
            return -1
        }
        // Compare dates
        return b.updatedAt.getTime() - a.updatedAt.getTime()
    })
})

const onCreateConversation = async () => {
    const newConversation = await createConversation('Untitled Conversation')
    await switchConversation(newConversation.id)
}

const isKnowledgeManagerOpen = ref(false)
const onOpenKnowledgeManager = () => {
    navigateTo('/knowledge')
    // isKnowledgeManagerOpen.value = true
}
</script>

<template>
    <div
        flex flex-col h-full
        px-2 pr-4
    >
        <div uppercase font-bold text-13px text-primary-600 dark:text-primary-400 my-3 flex items-center>
            <div>
                Chats
            </div>
            <div
                v-if="conversationList?.length" ml-auto text-11px
                text-gray-5 dark:text-gray-4
            >
                {{ conversationList?.length }} conversations
            </div>
        </div>
        <div max-h-100 overflow-y-auto overflow-x-hidden w-full pb-2>
            <ConversationTab
                v-for="conversation in conversationsSortedByUpdatedAt"
                :key="conversation.id"
                :conversation="conversation"
            />
        </div>
        <div flex items-center mt-2 gap-2>
            <UButton secondary icon="i-tabler-plus" grow @click="onCreateConversation">
                New chat
            </UButton>
            <GpLongPressButton
                :duration="1500"
                icon="i-tabler-arrow-bar-to-up"
                progress-bar-style="bg-red/50"
                success-style="!ring-red"
                @success="clearConversations"
            >
                Clear
            </GpLongPressButton>
        </div>

        <!-- TODO: Add knowledge section -->
        <!-- <div uppercase font-bold text-13px text-primary mb-2 mt-6>
            Knowledge
        </div>

        <div>
            <UButton w-full icon="i-tabler-brain" @click="onOpenKnowledgeManager">
                <span>Manage</span>
            </UButton>
        </div> -->

        <div mt-auto>
            <SidebarItem
                text-4
                :active="route.path.startsWith('/settings')"
                @click="navigateTo('/settings')"
            >
                <span i-tabler-settings mr-2 text-primary-500 dark:text-primary-400 text-5 />
                <span>
                    Settings
                </span>
                <ColorModeToggle ml-auto />
            </SidebarItem>
            <SidebarApiKeyAlert
                v-if="!apiKey"
                mt-3
                @click="navigateTo('/settings/api-key')"
            />
            <div
                text-color-lighter my-6 text-5 tracking--1px w-full
                flex-col flex justify-center items-center
            >
                <div font-black>
                    geppeto
                </div>
                <div text-3 tracking-wide flex items-center gap-1 op-60>
                    made with <div i-tabler-heart-filled text-red /> by Caret
                </div>
            </div>
        </div>
    </div>
</template>
