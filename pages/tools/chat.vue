<template>
  <div class="pb-16 pattern min-h-[100dvh]">
    <div class="container relative mx-auto px-4 min-h-screen flex flex-col overflow-hidden">
      <div class="flex justify-between items-center chat-header rounded-xl my-2 p-2 sm:p-0 w-full lg:w-3/4 xl:w-1/2 mx-auto">
        <!-- Back arrow -->
        <div class="shrink-0 pl-1">
          <nuxt-link to="/" title="back" class="back-btn w-8 h-8 rounded-lg flex justify-center items-center">
            <i class="icon icon-arrow-ios-back icon-16"></i>
          </nuxt-link>
        </div>
        
        <!-- Chat with Dara text and Dara image -->
        <div class="flex items-center justify-center text-white flex-1 min-w-0">
          <div class="flex items-center justify-center gap-2">
            <span class="text-base sm:text-xl md:text-2xl truncate">Chat with Dara</span>
            <img class="block w-8 h-8 sm:w-10 sm:h-10 rounded-full shrink-0" :src="daraAvatar" draggable="false" />
          </div>
        </div>
      </div>
      <!-- Chat content -->
      <div class="bg-white/40 border border-[#5e2c57]/50 rounded-xl p-2 relative flex-1 flex flex-col mb-2 w-full lg:w-3/4 xl:w-1/2 mx-auto">
        <div class="flex-1">
          <ul class="flex flex-col flex-1 overflow-y-auto hide-sb main-chat" ref="chatBody">
            <li v-for="message in messages" :key="message.id" :class="['flex items-start max-w-[85%] sm:max-w-md my-2', message.role === 'user' ? 'self-end flex-row-reverse' : 'self-start']">
              <img class="w-8 h-8 sm:w-10 sm:h-10 rounded-full shrink-0" :class="message.role === 'user' ? 'ml-2 sm:ml-3' : 'mr-2 sm:mr-3'" :src="message.role === 'ai' ? daraAvatar : userAvatar"/>
              <div class="rounded-2xl sm:rounded-lg px-3 py-2 sm:p-2 text-white break-words" :class="[message.role === 'ai' ? 'bg-[#5e2c57]' : 'bg-[#1a1a2e]']">
                <p class="text-sm sm:text-base leading-relaxed whitespace-pre-wrap">{{ message.text }}</p>
              </div>
            </li>
            <li v-if="typing" class="flex flex-col max-w-[85%] sm:max-w-md self-start items-start">
              <img class="w-8 h-8 sm:w-10 sm:h-10 rounded-full" :src="daraAvatar" />
              <div class="bg-[#5e2c57] rounded-2xl sm:rounded-lg px-3 py-2 sm:p-2 mt-1 text-white">
                <p class="text-sm sm:text-base">typing ...</p>
              </div>
            </li>
          </ul>
        </div>
        <form @submit.prevent="sendMessage" class="px-2 pb-2 sm:px-4 sm:pb-4">
          <div class="flex items-end gap-2 bg-white rounded-xl border border-gray-200 p-1.5 sm:p-2">
            <textarea
              v-model="message"
              @keydown="handleKeydown"
              class="flex-1 p-2 sm:p-3 resize-none text-black text-sm sm:text-base outline-none min-h-[40px] max-h-[120px]"
              rows="1"
              placeholder="your message"
            ></textarea>
            <button
              :disabled="typing || !message"
              :class="[typing ? 'cursor-not-allowed opacity-40' : 'hover:opacity-75']"
              type="submit"
              title="send"
              class="flex justify-center items-center bg-[#af3d50] text-white w-9 h-9 sm:w-10 sm:h-10 rounded-xl cursor-pointer transition-opacity shrink-0"
            >
              <i class="icon icon-lg rotate-45 icon-paper-plane"></i>
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import {nanoid} from 'nanoid'
import {reactive, ref, unref} from 'vue'
export default {
  async setup() {
    useHead({
      title: 'Dara'
    })
    definePageMeta({
      pageTransition: {name: 'fade', mode: 'out-in'}
    })

    const runtimeConfig = useRuntimeConfig()
    const toast = useToast()
    const daraAvatar = ref('/dara.png')
    const userAvatar = ref('/user.png')
    const session = nanoid(7)
    let chatBody = ref(null)
    const messages = reactive([
      {
        id: nanoid(8),
        role: 'ai',
        text: 'Hi I\'m Dara, How can I help you today ?'
      }
    ])
    let message = ref('')
    let typing = ref(false)
    const layoutConfig = {
      header: 0,
      footer: 1
    }
    const scrollToEndOfChat = () => {
      setTimeout(() => {
        unref(chatBody).scrollTop = unref(chatBody).scrollHeight
      }, 100)
    }
    const sendMessage = async () => {
      messages.push({
        id: nanoid(8),
        role: 'user',
        text: message.value
      })
      typing.value = true
      scrollToEndOfChat();
      
      const api = useApi()
      const res = await api.fetch(`${runtimeConfig.public.backend}/api/chat`, {
        method: 'POST',
        body: {
          question: message._value,
          session
        }
      }).catch((err) => {
        toast.show({type: 'error', time: 2, title: 'Error', message: 'The message could not be sent'})
        messages.pop()
      })
      if (res?.answer) {
        typing.value = false
        message.value = ''
        messages.push({
          id: nanoid(8),
          role: 'ai',
          text: res?.answer
        })
        scrollToEndOfChat();
      } else {
        typing.value = false
      }
    }

    const handleKeydown = (event) => {
      if (event.key === 'Enter' && !event.shiftkey) {
        event.preventDefault()
        sendMessage()
      }
    }

    return {
      messages,
      daraAvatar,
      userAvatar,
      message,
      chatBody,
      sendMessage,
      layoutConfig,
      typing,
      handleKeydown
    }
  },
  created() {
    const { header, footer } = this.layoutConfig;
    this.$route.meta.header = header;
    this.$route.meta.footer = footer;
  }
}
</script>

<style scoped>
.main-chat {
  max-height: calc(100vh - 18rem);
}
@media screen and (max-width: 640px) {
  .main-chat {
    max-height: calc(100dvh - 14rem);
  }
}
@media screen and (max-width: 380px) {
  .main-chat {
    max-height: calc(100dvh - 13rem);
  }
}
.hide-sb {
  -ms-overflow-style: none;
  scrollbar-width: none;
}
.hide-sb::-webkit-scrollbar {
  display: none;
}
.chat-header {
  background-color: #5e2c57;
}
.back-btn {
  background-color: #af3d50;
  color: #fff;
}
</style>