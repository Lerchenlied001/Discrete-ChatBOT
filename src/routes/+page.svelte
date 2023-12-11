<script lang="ts">
	import ChatMessage from '$lib/components/ChatMessage.svelte'
	import type { ChatCompletionRequestMessage } from 'openai'
	import { SSE } from 'sse.js'

	let query: string = ''
	let answer: string = ''
	let loading: boolean = false
	let chatMessages: ChatCompletionRequestMessage[] = []
	let scrollToDiv: HTMLDivElement

	function scrollToBottom() {
		setTimeout(function () {
			scrollToDiv.scrollIntoView({ behavior: 'smooth', block: 'end', inline: 'nearest' })
		}, 100)
	}

	const handleSubmit = async () => {
		loading = true
		chatMessages = [...chatMessages, { role: 'user', content: query }]

		const eventSource = new SSE('/api/chat', {
			headers: {
				'Content-Type': 'application/json'
			},
			payload: JSON.stringify({ messages: chatMessages })
		})

		query = ''

		eventSource.addEventListener('error', handleError)

		eventSource.addEventListener('message', (e) => {
			scrollToBottom()
			try {
				loading = false
				if (e.data === '[DONE]') {
					chatMessages = [...chatMessages, { role: 'assistant', content: answer }]
					answer = ''
					return
				}

				const completionResponse = JSON.parse(e.data)
				const [{ delta }] = completionResponse.choices

				if (delta.content) {
					answer = (answer ?? '') + delta.content
				}
			} catch (err) {
				handleError(err)
			}
		})
		eventSource.stream()
		scrollToBottom()
	}

	function handleError<T>(err: T) {
		loading = false
		query = ''
		answer = ''
		console.error(err)
	}
</script>

<div class="hero min-h-0 bg-gray-800 w-full lg:w-5/3">
  <div class="hero-content flex-col lg:flex-row">
    <img src="https://img.freepik.com/premium-vector/knights-badge-logo-design_31492-57.jpg?w=2000" class="mw-25 h-20 rounded-full" />
    <div>
      <h1 class="text-3xl font-bold w-full text-center" style="color: white;"><u><strong>F.R.E.A</strong></u></h1>
      <p class="text-1sm font-italic" style="color: white;"><strong>Mathematical Recursion Education Platform</strong></p>
    </div>
  </div>
</div>
<br/>
<br/>
  <div class="h-[500px] w-full bg-gray-800 rounded-md p-4 overflow-y-auto flex flex-col gap-4">
    <div class="flex flex-col gap-4">
      <ChatMessage type="assistant" message="Welcome to the exciting world of Mathematical Recursion with our expert Chatbot! Dive into interactive lessons tailored to your skill level, exploring the core principles and applications of recursion in both mathematics and computer science. This journey promises to challenge your mind and enhance your problem-solving skills, making learning both fun and accessible. Get ready to explore the intricate wonders of recursion and discover its fascinating role in the world of algorithms and beyond!. Press the 'QUIZ' button if you are confident enough to take the Quiz." />
      {#each chatMessages as message}
        <ChatMessage type={message.role} message={message.content} />
      {/each}
      {#if answer}
        <ChatMessage type="assistant" message={answer} />
      {/if}
      {#if loading}
        <ChatMessage type="assistant" message="Processing.." />
      {/if}
    </div>
    <div class="" bind:this={scrollToDiv} />
  </div>
  <br/>
  <form
    class="flex w-full rounded-md gap-4 bg-gray-800 p-4"
    on:submit|preventDefault={() => handleSubmit()}
  >
    <input type="text" class="input input-bordered w-full" bind:value={query} />
    <button type="submit" class="btn btn-error">Send</button>
  </form>

