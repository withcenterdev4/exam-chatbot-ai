<script lang="ts">
	import { getFirestore, query, addDoc, collection, onSnapshot } from 'firebase/firestore';
	import { onMount, tick } from 'svelte';
	const db = getFirestore();

	type MSG = {
		prompt: string;
		gpt: string;
		palm: string;
	};

	let messages: Record<string, MSG> = {};
	let prompt: string = '';

	// on Render, initialize a realtime snapshot of the firestore database
	onMount(() => {
		const q = query(collection(db, 'chatbot'));
		let unsub = onSnapshot(q, async (qS) => {
			qS.forEach((doc) => {
				messages[doc.id] = doc.data() as MSG;
			});

			// Scrolls to the bottom of the chat
			await tick();
			document.querySelector('.bottom')?.scrollIntoView();
		});

		return () => {
			unsub();
		};
	});

	// Sending a prompt into the Firestore
	const submit = async () => {
		await addDoc(collection(db, 'chatbot'), { prompt });
		prompt = '';
	};
</script>

<main>
	<h1>Chatbot AI</h1>
	<form on:submit|preventDefault={submit}>
		<ul>
			{#each Object.keys(messages) as key}
				<li class="user">
					{messages[key].prompt}
				</li>
				{#if messages[key].palm && messages[key].gpt}
					<li class="palm">
						<span class="AI-palm">PaLM</span><span class="response">{messages[key].palm}</span>
					</li>
					<li class="gpt">
						<span class="AI-gpt">GPT</span><span class="response">{messages[key].gpt}</span>
					</li>
				{:else if messages[key].palm}
					<li class="palm">
						<span class="AI-palm">PaLM</span><span class="response">{messages[key].palm}</span>
					</li>
					<li class="gpt"><span class="AI-gpt">GPT</span><span class="no-res">Loading...</span></li>
				{:else if messages[key].gpt}
					<li class="palm">
						<span class="AI-palm">PaLM</span><span class="no-res">Loading...</span>
					</li>
					<li class="gpt">
						<span class="AI-gpt">GPT</span><span class="response">{messages[key].gpt}</span>
					</li>
				{:else}
					<li class="loading">Generating response...</li>
				{/if}
			{/each}
			<li class="bottom" />
		</ul>
		<div class="inputfield">
			<input type="text" bind:value={prompt} placeholder="Type a message..." required /><button
				type="submit">Send</button
			>
		</div>
	</form>
</main>

<style>
	* {
		box-sizing: border-box;
	}

	main {
		height: 100vh;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}

	form {
		height: 100%;
		width: 100%;
		max-height: 900px;
		max-width: 700px;
		border: 1px solid black;
		display: flex;
		flex-direction: column;
	}

	ul {
		list-style-type: none;
		overflow-y: auto;
		flex: 1;
		display: flex;
		flex-direction: column;
		gap: 20px;
		padding: 15px;
		margin: 0;
	}

	.palm,
	.gpt {
		color: white;
		align-self: flex-start;
		max-width: 80%;
		text-align: left;
		display: flex;
		gap: 20px;
	}

	.gpt .AI-gpt,
	.palm .AI-palm {
		font-size: 2rem;
		color: black;
	}

	.palm .response {
		background-color: rgba(255, 0, 0, 0.569);
	}

	.gpt .response {
		background-color: rgba(0, 128, 0, 0.487);
	}

	.gpt .response,
	.palm .response,
	.loading,
	.no-res {
		font-size: 2rem;
		padding: 10px;
		border-radius: 10px;
	}

	.user {
		font-size: 2rem;
		padding: 10px;
		border-radius: 10px;
		background-color: rgba(0, 0, 255, 0.458);
		color: white;
		align-self: flex-end;
		text-align: right;
		max-width: 80%;
		word-wrap: break-word;
	}

	.inputfield {
		display: flex;
		border-top: 2px solid black;
		padding: 10px 0;
	}

	input {
		flex: 1;
		width: 100%;
		font-size: 2.5rem;
		padding: 10px;
		border: none;
		margin-left: 10px;
	}

	.inputfield button {
		padding: 10px;
		font-size: 2rem;
	}

	.no-res {
		background-color: red;
		color: white;
		text-decoration: underline;
		padding: 0 10px;
	}

	.loading {
		background-color: blueviolet;
		color: white;
	}

	button[type='submit'] {
		border: none;
		border-radius: 20px;
		margin: 0 10px;
		cursor: pointer;
	}
</style>
