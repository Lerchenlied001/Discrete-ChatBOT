<script lang="ts">
  import type { ChatCompletionRequestMessageRoleEnum } from 'openai'
  export let type: ChatCompletionRequestMessageRoleEnum
  export let message: string
</script>

<div class="chat {type === 'user' ? 'chat-end' : 'chat-start'} justify-end">
  <div class="chat-image avatar">
    <div class="w-14 rounded-full">
      <img src="https://i.imgur.com/PowAuuU.png" alt="{type} avatar"/>
    </div>
  </div>
  <div class="chat-header" style="color: red; font-weight: bold;">
    {type === 'user' ? 'Me' : 'DIS10 BOT'}
  </div>
  <div class="chat-bubble {type === 'user' ? 'chat-bubble-success' : 'chat-bubble-error'}">
    {message}
  </div>
</div>