<script lang="ts" setup>
const props = defineProps<{ modelValue: string }>()
const emit = defineEmits(['update:modelValue', 'send'])

const { apiKey } = useSettings()
const { isTypingInCurrentConversation } = useConversations()
const textarea = ref()
const isLogged = computed(() => Boolean(apiKey.value))

const onSend = () => {
    if (!isTypingInCurrentConversation.value && props.modelValue) {
        emit('send', props.modelValue)
        emit('update:modelValue', '')
    }
}

const onType = (event?: any) => {
    if (!textarea.value) {
        return
    }
    textarea.value.style.height = '1.75rem'
    textarea.value.style.height = `${Math.min(textarea.value.scrollHeight, 100)}px`

    if (event) {
        emit('update:modelValue', (event.target as any).value)
    }
}
const handleEnter = (e: KeyboardEvent) => {
    if (e.shiftKey) {
        return
    }
    e.preventDefault()
    onSend()
    textarea.value.style.height = '1.75rem'
}
</script>

<template>
    <div
        relative p-2 pb-2px sm:p-3 sm:pb-2
        pr-11 sm:pr-20
        text-gray-600 placeholder:text-gray-400
        placeholder:transition
        class="focus-within:placeholder:translate-x-2 bg-gray-1/80 dark:bg-white/5 dark:ring-white/5" ring-2
        ring-inset rounded-3 shadow-inset
        shadow ring-gray-100 focus-within:ring-primary focus-within:shadow-md
        transition
        backdrop-filter backdrop-blur-2px
        :class="[
            !isLogged ? 'cursor-not-allowed' : '',
        ]"
    >
        <textarea
            ref="textarea"
            :value="modelValue"
            w-full
            text-12px sm:text-16px
            outline-none overflow-y-auto bg-transparent overflow-x-hidden
            placeholder="Type your message here..." leading-6
            relative z-2 resize-none b-0 h-28px sm:h-7
            dark:placeholder:text-gray-4
            text-gray-8 dark:text-gray-1
            focus:placeholder:translate-x-6px placeholder:transition-all
            :disabled="!isLogged"
            class="!font-[ColfaxAI]"
            :class="[
                !isLogged ? 'cursor-not-allowed' : '',
            ]"
            @input="onType"
            @keydown.enter="handleEnter"
        />
        <div
            absolute right-2
            bottom-8px sm:bottom-10px
            z-3
        >
            <UButton
                :disabled="!isLogged || !modelValue"
                text-12px
                @click="onSend"
            >
                <div
                    text-3 sm:text-5
                    :class="[
                        !isTypingInCurrentConversation ? 'i-tabler-send' : 'i-eos-icons-bubble-loading',
                    ]"
                />
            </UButton>
        </div>
    </div>
</template>

<style>
.slide-in-bottom-enter-active, .slide-in-bottom-leave-active {
    transition: all .2s ease;
}

.slide-in-bottom-enter, .slide-in-bottom-leave-to {
    transform: translateY(100%) scaleY(0);
    opacity: 0;
}
</style>
