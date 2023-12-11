<script lang="ts">
  import type { ChatCompletionRequestMessageRoleEnum } from 'openai'
  export let type: ChatCompletionRequestMessageRoleEnum
  export let message: string
</script>

<div class="chat {type === 'user' ? 'chat-end' : 'chat-start'} justify-end">
  <div class="chat-image avatar">
    <div class="w-14 rounded-full">
      <img src="https://images-platform.99static.com//9pruL3GMSNpmFA6rrYtb8tlGCeU=/86x1262:1113x2290/fit-in/500x500/99designs-contests-attachments/131/131688/attachment_131688576" alt="{type} avatar"/>
    </div>
  </div>
  <div class="chat-header" style="color: white; font-weight: bold;">
    {type === 'user' ? 'Me' : 'DIS10 BOT'}
  </div>
  <div class="chat-bubble {type === 'user' ? 'chat-bubble-success' : 'chat-bubble-error'}">
    {message}
  </div>
</div>