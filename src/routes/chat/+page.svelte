<script lang="ts">
	import { onMount } from 'svelte';

	function generateUUID() {
		let dt = new Date().getTime();
		const uuid = 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, (c) => {
			const r = (dt + Math.random() * 16) % 16 | 0;
			dt = Math.floor(dt / 16);
			return (c === 'x' ? r : (r & 0x3) | 0x8).toString(16);
		});
		return uuid;
	}

	/**
	 * @type {any[]}
	 */
	let messages: any = [];

	let message = '';

	function SendMessage() {
		if (message !== '') {
			fetch('http://localhost:1323/api/v1/SendMessage', {
				method: 'POST',
				headers: {
					'Content-Type': 'application/json',
					Authorization: 'Bearer ' + localStorage.getItem('token')
				},
				body: JSON.stringify({
					message: message
				})
			})
				.then((res) => res.json())
				.then((res) => {
					if (res.status === 200) {
						message = '';
					}
				});
		}
	}

	let parsedValue = {};

	onMount(() => {
		fetch('http://localhost:1323/api/v1/GetChat', {
			method: 'GET',
			headers: {
				'Content-Type': 'application/json',
				Authorization: 'Bearer ' + localStorage.getItem('token')
			}
		})
			.then((res) => res.json())
			.then((res) => {
				if (res.status === 200) {
					messages = res.data;
				}
			});
		let id = generateUUID();
		const source = new EventSource(`http://localhost:1323/api/v1/chat?id=${id}`);
		source.onopen = function (event) {
			console.log('open');
		};
		source.onerror = function (event) {
			console.log('error');
		};
		source.addEventListener('NewMessage', function (event) {
			// let data = JSON.parse(event.data);
			$: parsedValue = JSON.parse(event.data);
			console.log(parsedValue);
			UpdateMessage(parsedValue);
		});
	});
	function UpdateMessage(parsedValue: any) {
		const data = JSON.parse(parsedValue);
		let response = {
			user_name: data.user_name,
			message: data.message
		};
		console.log(messages)
		$: messages = [...messages, response];
	}
</script>

<div class="flex flex-row h-screen">
	<!-- Columna de usuarios conectados -->
	<div class="w-1/4 bg-gray-100 border-r border-gray-200 p-4">
		<h2 class="text-lg font-medium mb-4">Usuarios conectados</h2>
		<ul>
			<li class="mb-2 py-1 px-2 hover:bg-gray-200 hover:text-gray-700 cursor-pointer">Juan</li>
			<li class="mb-2 py-1 px-2 hover:bg-gray-200 hover:text-gray-700 cursor-pointer">María</li>
			<li class="mb-2 py-1 px-2 hover:bg-gray-200 hover:text-gray-700 cursor-pointer">Pedro</li>
			<li class="mb-2 py-1 px-2 hover:bg-gray-200 hover:text-gray-700 cursor-pointer">Ana</li>
		</ul>
	</div>

	<!-- Columna de mensajes del chat -->
	<div class="flex-1 p-4">
		<h2 class="text-lg font-medium mb-4">Chat</h2>
		<div class="overflow-y-auto h-4/5">
			{#each messages as message}
				{#if message.message !== ''}
					<div class="mb-4">
						<span class="font-medium text-blue-600">{message.user_name}:</span>
						<span>{message.message}</span>
					</div>
				{/if}
			{/each}
		</div>
		<!-- Textarea para escribir mensajes nuevos -->
		<form class="mt-4" on:submit={SendMessage}>
			<div class="flex">
				<textarea bind:value={message} class="flex-1 border border-gray-300 p-2 rounded-lg mr-2" />
				<button type="submit" class="bg-blue-500 text-white px-4 py-2 rounded-lg">Enviar</button>
			</div>
		</form>
	</div>
</div>
